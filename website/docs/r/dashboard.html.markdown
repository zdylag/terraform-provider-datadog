---
layout: "datadog"
page_title: "Datadog: datadog_dashboard"
sidebar_current: "docs-datadog-resource-dashboard"
description: |-
  Provides a Datadog dashboard resource. This can be used to create and manage dashboards.
---

# datadog_dashboard

Provides a Datadog dashboard resource. This can be used to create and manage Datadog dashboards.

~> **Note:** This resource uses the new [Dashboard API](https://docs.datadoghq.com/api/#dashboards) which adds new features like better validation and support for the [Group widget](https://docs.datadoghq.com/graphing/widgets/group/). Additionally, this resource unifies [`datadog_timeboard`](timeboard.html) and [`datadog_screenboard`](screenboard.html) resources to allow you to manage all of your dashboards using a single format.


## Example Usage: Create a new Datadog dashboard - Ordered layout

```hcl
resource "datadog_dashboard" "ordered_dashboard" {
  title         = "Ordered Layout Dashboard"
  description   = "Created using the Datadog provider in Terraform"
  layout_type   = "ordered"
  is_read_only  = true

  widget {
    alert_graph_definition {
      alert_id = "895605"
      viz_type = "timeseries"
      title = "Widget Title"
      time = {
        live_span = "1h"
      }
    }
  }

  widget {
    alert_value_definition {
      alert_id = "895605"
      precision = 3
      unit = "b"
      text_align = "center"
      title = "Widget Title"
    }
  }

  widget {
    alert_value_definition {
      alert_id = "895605"
      precision = 3
      unit = "b"
      text_align = "center"
      title = "Widget Title"
    }
  }

  widget {
    change_definition {
      request {
        q = "avg:system.load.1{env:staging} by {account}"
        change_type = "absolute"
        compare_to = "week_before"
        increase_good = true
        order_by = "name"
        order_dir = "desc"
        show_present = true
      }
      title = "Widget Title"
      time = {
        live_span = "1h"
      }
    }
  }

  widget {
    distribution_definition {
      request {
        q = "avg:system.load.1{env:staging} by {account}"
        style {
          palette = "warm"
        }
      }
      title = "Widget Title"
      time = {
        live_span = "1h"
      }
    }
  }

  widget {
    check_status_definition {
      check = "aws.ecs.agent_connected"
      grouping = "cluster"
      group_by = ["account", "cluster"]
      tags = ["account:demo", "cluster:awseb-ruthebdog-env-8-dn3m6u3gvk"]
      title = "Widget Title"
      time = {
        live_span = "1h"
      }
    }
  }

  widget {
    heatmap_definition {
      request {
        q = "avg:system.load.1{env:staging} by {account}"
        style {
          palette = "warm"
        }
      }
      yaxis {
        min = 1
        max = 2
        include_zero = true
        scale = "sqrt"
      }
      title = "Widget Title"
      time = {
        live_span = "1h"
      }
    }
  }

  widget {
    hostmap_definition {
      request {
        fill {
          q = "avg:system.load.1{*} by {host}"
        }
        size {
          q = "avg:memcache.uptime{*} by {host}"
        }
      }
      node_type= "container"
      group = ["host", "region"]
      no_group_hosts = true
      no_metric_hosts = true
      scope = ["region:us-east-1", "aws_account:727006795293"]
      style {
        palette = "yellow_to_green"
        palette_flip = true
        fill_min = "10"
        fill_max = "20"
      }
      title = "Widget Title"
    }
  }

  widget {
    note_definition {
      content = "note text"
      background_color = "pink"
      font_size = "14"
      text_align = "center"
      show_tick = true
      tick_edge = "left"
      tick_pos = "50%"
    }
  }

  widget {
    query_value_definition {
      request {
        q = "avg:system.load.1{env:staging} by {account}"
        aggregator = "sum"
        conditional_formats {
          comparator = "<"
          value = "2"
          palette = "white_on_green"
        }
        conditional_formats {
          comparator = ">"
          value = "2.2"
          palette = "white_on_red"
        }
      }
      autoscale = true
      custom_unit = "xx"
      precision = "4"
      text_align = "right"
      title = "Widget Title"
      time = {
        live_span = "1h"
      }
    }
  }

  widget {
    scatterplot_definition {
      request {
        x {
          q = "avg:system.cpu.user{*} by {service, account}"
          aggregator = "max"
        }
        y {
          q = "avg:system.mem.used{*} by {service, account}"
          aggregator = "min"
        }
      }
      color_by_groups = ["account", "apm-role-group"]
      xaxis {
        include_zero = true
        label = "x"
        min = "1"
        max = "2000"
        scale = "pow"
      }
      yaxis {
        include_zero = false
        label = "y"
        min = "5"
        max = "2222"
        scale = "log"
      }
      title = "Widget Title"
      time = {
        live_span = "1h"
      }
    }
  }

  widget {
    timeseries_definition {
      request {
        q= "avg:system.cpu.user{app:general} by {env}"
        display_type = "line"
        style {
          palette = "warm"
          line_type = "dashed"
          line_width = "thin"
        }
        metadata {
          expression = "avg:system.cpu.user{app:general} by {env}"
          alias_name = "Alpha"
        }
      }
      request {
        log_query {
          index = "mcnulty"
          compute = {
            aggregation = "count"
            facet = "@duration"
            interval = 5000
          }
          search = {
            query = "status:info"
          }
          group_by {
            facet = "host"
            limit = 10
            sort = {
              aggregation = "avg"
              order = "desc"
              facet = "@duration"
            }
          }
        }
        display_type = "area"
      }
      request {
        apm_query {
          index = "apm-search"
          compute = {
            aggregation = "count"
            facet = "@duration"
            interval = 5000
          }
          search = {
            query = "type:web"
          }
          group_by {
            facet = "resource_name"
            limit = 50
            sort = {
              aggregation = "avg"
              order = "desc"
              facet = "@string_query.interval"
            }
          }
        }
        display_type = "bars"
      }
      request {
        process_query {
          metric = "process.stat.cpu.total_pct"
          search_by = "error"
          filter_by = ["active"]
          limit = 50
        }
        display_type = "area"
      }
      marker {
        display_type = "error dashed"
        label = " z=6 "
        value = "y = 4"
      }
      marker {
        display_type = "ok solid"
        value = "10 < y < 999"
        label = " x=8 "
      }
      title = "Widget Title"
      time = {
        live_span = "1h"
      }
    }
  }

  widget {
    toplist_definition {
      request {
        q= "avg:system.cpu.user{app:general} by {env}"
        conditional_formats {
          comparator = "<"
          value = "2"
          palette = "white_on_green"
        }
        conditional_formats {
          comparator = ">"
          value = "2.2"
          palette = "white_on_red"
        }
      }
      title = "Widget Title"
    }
  }

  widget {
    group_definition {
      layout_type = "ordered"
      title = "Group Widget"

      widget {
        note_definition {
          content = "cluster note widget"
          background_color = "pink"
          font_size = "14"
          text_align = "center"
          show_tick = true
          tick_edge = "left"
          tick_pos = "50%"
        }
      }

      widget {
        alert_graph_definition {
          alert_id = "123"
          viz_type = "toplist"
          title = "Alert Graph"
          time = {
            live_span = "1h"
          }
        }
      }
    }
  }

  template_variable {
    name   = "var_1"
    prefix = "host"
    default = "aws"
  }
  template_variable {
    name   = "var_2"
    prefix = "service_name"
    default = "autoscaling"
  }
}
```
## Example Usage: Create a new Datadog dashboard - Free layout

```hcl
resource "datadog_dashboard" "free_dashboard" {
  title         = "Free Layout Dashboard"
  description   = "Created using the Datadog provider in Terraform"
  layout_type   = "free"
  is_read_only  = false

  widget {
    event_stream_definition {
      query = "*"
      event_size = "l"
      title = "Widget Title"
      title_size = 16
      title_align = "left"
      time = {
        live_span = "1h"
      }
    }
    layout = {
      height = 43
      width = 32
      x = 5
      y = 5
    }
  }

  widget {
    event_timeline_definition {
      query = "*"
      title = "Widget Title"
      title_size = 16
      title_align = "left"
      time = {
        live_span = "1h"
      }
    }
    layout = {
      height = 9
      width = 65
      x = 42
      y = 73
    }
  }

  widget {
    free_text_definition {
      text = "free text content"
      color = "#d00"
      font_size = "88"
      text_align = "left"
    }
    layout = {
      height = 20
      width = 30
      x = 42
      y = 5
    }
  }

  widget {
    iframe_definition {
      url = "http://google.com"
    }
    layout = {
      height = 46
      width = 39
      x = 111
      y = 8
    }
  }

  widget {
    image_definition {
      url = "https://images.pexels.com/photos/67636/rose-blue-flower-rose-blooms-67636.jpeg?auto=compress&cs=tinysrgb&h=350"
      sizing = "fit"
      margin = "small"
    }
    layout = {
      height = 20
      width = 30
      x = 77
      y = 7
    }
  }

  widget {
    log_stream_definition {
      logset = "19"
      query = "error"
      columns = ["core_host", "core_service", "tag_source"]
    }
    layout = {
      height = 36
      width = 32
      x = 5
      y = 51
    }
  }

  widget {
    manage_status_definition {
      color_preference = "text"
      count = 50
      display_format = "countsAndList"
      hide_zero_counts = true
      query = "type:metric"
      sort = "status,asc"
      start = 0
      title = "Widget Title"
      title_size = 16
      title_align = "left"
    }
    layout = {
      height = 40
      width = 30
      x = 112
      y = 55
    }
  }

  widget {
    trace_service_definition {
      display_format = "three_column"
      env = "datad0g.com"
      service = "alerting-cassandra"
      show_breakdown = true
      show_distribution = true
      show_errors = true
      show_hits = true
      show_latency = false
      show_resource_list = false
      size_format = "large"
      span_name = "cassandra.query"
      title = "alerting-cassandra #env:datad0g.com"
      title_align = "center"
      title_size = "13"
      time = {
        live_span = "1h"
      }
    }
    layout = {
      height = 38
      width = 67
      x = 40
      y = 28
    }
  }

  template_variable {
    name   = "var_1"
    prefix = "host"
    default = "aws"
  }
  template_variable {
    name   = "var_2"
    prefix = "service_name"
    default = "autoscaling"
  }
}
```

## Argument Reference

The following arguments are supported:

- `title` - (Required) Title of the dashboard.
- `widget` - (Required) Nested block describing a widget. The structure of this block is described [below](dashboard.html#nested-widget-blocks). Multiple `widget` blocks are allowed within a `datadog_dashboard` resource
- `layout_type` - (Required) Layout type of the dashboard. Available values are: `ordered` (previous timeboard) or `free` (previous screenboard layout).
- `description` - (Optional) Description of the dashboard.
- `is_read_only` - (Optional) Whether this dashboard is read-only. If `true`, only the author and admins can make changes to it.
- `notify_list` - (Optional) List of handles of users to notify when changes are made to this dashboard.
- `template_variables` - (Optional) Nested block describing a template variable. The structure of this block is described [below](dashboard.html#nested-template_variable-blocks). Multiple template_variable blocks are allowed within a `datadog_dashboard` resource.

### Nested `widget` blocks

Nested `widget` blocks have the following structure:

- `layout` - (Required for widgets in dashboards with `free` layout_type only). The structure of this block is described [below](dashboard.html#nested-widget-layout-blocks)
- A widget should have exactly one of the following nested blocks describing the widget definition:
  - `alert_graph_definition`: The definition for a Alert Graph widget. Exactly one nested block is allowed with the following structure:
      - `alert_id`: (Required) The ID of the monitor used by the widget.
      - `viz_type`: (Required) Type of visualization to use when displaying the widget. Either "timeseries" or "toplist".
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right"
      - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `alert_value_definition`: The definition for an Alert Value widget. Exactly one nested block is allowed with the following structure:
      - `alert_id`: (Required) The ID of the monitor used by the widget.
      - `precision`: (Optional) The precision to use when displaying the value. Use "*" for maximum precision.
      - `unit`: (Optional) The unit for the value displayed in the widget.
      - `text_align`: (Optional) The alignment of the text in the widget.
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right"
  - `change_definition`: The definition for a Change widget. Exactly one nested block is allowed with the following structure:
      - `request`: (Required) Nested block describing the request to use when displaying the widget. Multiple request blocks are allowed with the following structure:
          - `q`: (Required) The metric query to use in the widget.
          - `change_type`: (Optional) Whether to show absolute or relative change. One of "absolute", "relative".
          - `compare_to` - (Optional) Choose from when to compare current data to. One of "hour_before", "day_before", "week_before" or "month_before".
          - `increase_good` - (Optional) Boolean indicating whether an increase in the value is good (thus displayed in green) or not (thus displayed in red).
          - `order_by` - (Optional) One of "change", "name", "present" (present value) or "past" (past value).
          - `order_dir` - (Optional) Either "asc" (ascending) or "desc" (descending).
          - `show_present` - (Optional) If set to "true", displays current value.
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
      - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `check_status_definition`: The definition for a Check Status widget. Exactly one nested block is allowed with the following structure:
      - `check` - (Optional) The check to use in the widget.
      - `grouping` - (Optional) Either "check" or "cluster", depending on whether the widget should use a single check or a cluster of checks.
      - `group` - (Optional) The check group to use in the widget.
      - `group_by` - (Optional) When grouping = "cluster", indicates a list of tags to use for grouping.
      - `tags` - (Optional) List of tags to use in the widget.
      - `title`: (Optional) The title of the widget.
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
      - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `distribution_definition`: The definition for a Distribution widget. Exactly one nested block is allowed with the following structure:
      - `request`: (Required) Nested block describing the request to use when displaying the widget. Multiple request blocks are allowed with the following structure:
          - `q`: (Required) The metric query to use in the widget.
          - `style` - (Optional) Style of the widget graph. One nested block is allowed with the following structure:
              - `palette` - (Optional) Color palette to apply to the widget. The available options are available here: https://docs.datadoghq.com/graphing/widgets/timeseries/#appearance.
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
      - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `event_stream_definition`: The definition for a Event Stream widget. Exactly one nested block is allowed with the following structure:
      - `query`: (Required) The query to use in the widget.
      - `event_size` - (Optional) The size of the events in the widget. Either "s" (small, title only) or "l" (large, full event).
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
      - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `event_timeline_definition`: The definition for a Event Timeline widget. Exactly one nested block is allowed with the following structure:
      - `text`: (Required) The query to use in the widget.
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
      - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `free_text_definition`: The definition for a Free Text. Exactly one nested block is allowed with the following structure:
      - `text` - (Required) The text to display in the widget.
      - `color` - (Optional) The color of the text in the widget.
      - `font_size` - (Optional, "note") The size of the text in the widget.
      - `text_align` - (Optional, "alert_value", "note") The alignment of the text in the widget.
  - `heatmap_definition`: The definition for a Heatmap widget. Exactly one nested block is allowed with the following structure:
      - `request`: (Required) Nested block describing the request to use when displaying the widget. Multiple request blocks are allowed with the following structure:
          - `q`: (Required) The metric query to use in the widget.
          - `style` - (Optional) Style of the widget graph. One nested block is allowed with the following structure:
              - `palette` - (Optional) Color palette to apply to the widget. The available options are available here: https://docs.datadoghq.com/graphing/widgets/timeseries/#appearance.
      - `yaxis`: (Optional) Nested block describing the Y-Axis Controls. The structure of this block is described [below](dashboard.html#nested-widget-axis-blocks)
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
      - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `hostmap_definition`: The definition for a Hostmap widget. Exactly one nested block is allowed with the following structure:
      - `request`: (Required) Nested block describing the request to use when displaying the widget. Multiple request blocks are allowed with the following structure:
          - `fill`: (Optional) The query used to fill the map. Exactly one nested block is allowed with the following structure:
              - `q`: (Required) The metric query to use in the widget.
          - `size`: (Optional) The query used to size the map. Exactly one nested block is allowed with the following structure:
              - `q`: (Required) The metric query to use in the widget.
      - `node_type` - (Optional) The type of node used. Either "host" or "container".
      - `no_metric_host` - (Optional) Boolean indicating whether to show nodes with no metrics.
      - `no_group_host` - (Optional) Boolean indicating whether to show ungrouped nodes.
      - `group` - (Optional) The list of tags to group nodes by.
      - `scope` - (Optional) The list of tags to filter nodes by.
      - `style` - (Optional) Style of the widget graph. One nested block is allowed with the following structure:
              - `palette` - (Optional) Color palette to apply to the widget. The available options are available here: https://docs.datadoghq.com/graphing/widgets/timeseries/#appearance.
              - `palette_flip` - (Optional) Boolean indicating whether to flip the palette tones.
              - `fill_min` - (Optional) Min value to use to color the map.
              - `fill_max` - (Optional) Max value to use to color the map.
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
  - `iframe_definition`: The definition for a Iframe widget. Exactly one nested block is allowed with the following structure:
      - `url` - (Rquired) The URL to use as a data source for the widget.
  - `image_definition`: The definition for a Image widget. Exactly one nested block is allowed with the following structure:
      - `url` - (Rquired) The URL to use as a data source for the widget.
      - `sizing` - (Optional) The preferred method to adapt the dimensions of the image to those of the widget. One of "center" (center the image in the tile), "zoom" (zoom the image to cover the whole tile) or "fit" (fit the image dimensions to those of the tile).
      - `margin` - (Optional) The margins to use around the image. Either "small" or "large".
  - `log_stream_definition`: The definition for a Log Stream widget. Exactly one nested block is allowed with the following structure:
      - `logset` - (Required) ID of the logset to use.
      - `query`: (Optional) The query to use in the widget.
      - `columns` - (Optional) Stringified list of columns to use. Example: `"["column1","column2","column3"]"`.
      - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
      - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `manage_status_definition`: The definition for a Manage Status widget. Exactly one nested block is allowed with the following structure:
      - `query`: (Required) The query to use in the widget.
      - `sort` - (Optional) The method to use to sort monitors. One of : "desc" or "asc".
      `count` - (Optional) The number of monitors to display.
      `start` - (Optional) The start of the list. Typically 0.
      - `display_format` - (Optional") The display setting to use. One of "counts", "list", or "countsAndList".
      - `color_preference` - (Optional") Whether to colorize text or background. One of "text", "background".
      - `hide_zero_counts` - (Optional") Boolean indicating whether to hide empty categories.
       - `title`: (Optional) The title of the widget.
      - `title_size`: (Optional) The size of the widget's title. Default is 16.
      - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
  - `note_definition`: The definition for a Note widget. Exactly one nested block is allowed with the following structure:
      - `content` - (Required) Content of the note
      - `background_color` - (Optional) Background color of the note.
      - `font_size` - (Optional) Size of the text.
      - `text_align` - (Optional) How to align the text on the widget. Available values are: `center`, `left`, or `right`.
      - `show_tick` - (Optional) Whether to show a tick or not.
      - `tick_pos` - (Optional") When tick = true, string with a percent sign indicating the position of the tick. Example: use tick_pos = "50%" for centered alignment.
      - `tick_edge` - (Optional") When tick = true, string indicating on which side of the widget the tick should be displayed. One of "bottom", "top", "left", "right".
  - `query_value_definition`: The definition for a Query Value widget. Exactly one nested block is allowed with the following structure:
        - `request`: (Required) Nested block describing the request to use when displaying the widget. Multiple request blocks are allowed with the following structure (exactly only one of `q`, `apm_query`, `log_query` or `process_query` is required within the request block):
            - `q`: (Optional) The metric query to use in the widget
            - `apm_query`: (Optional) The APM query to use in the widget. The structure of this block is described [below](dashboard.html#nested-apm_query-and-log_query-blocks).
            - `log_query`: (Optional) The log query to use in the widget. The structure of this block is described [below](dashboard.html#nested-apm_query-and-log_query-blocks).
            - `process_query`: (Optional) The process query to use in the widget. The structure of this block is described [below](dashboard.html#nested-process_query-blocks).
            - `conditional_formats` - (Optional) Conditional formats allow you to set the color of your widget content or background, depending on a rule applied to your data. Multiple request blocks are allowed. The structure of this block is described [below](dashboard.html#nested-widget-conditional_formats-blocks).
            - `aggregator` - (Optional) The aggregator to use for time aggregation. One of `avg`, `min`, `max`, `sum`, `last`.
        - `autoscale` - (Optional) Boolean indicating whether to automatically scale the tile.
        - `custom_unit` - (Optional) The unit for the value displayed in the widget
        - `precision` - (Optional) The precision to use when displaying the tile.
        - `text_align` - (Optional, "alert_value", "note") The alignment of the text in the widget.
        - `title`: (Optional) The title of the widget.
        - `title_size`: (Optional) The size of the widget's title. Default is 16.
        - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
        - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `scatterplot_definition`: The definition for a Scatterplot widget. Exactly one nested block is allowed with the following structure:
        - `request`: (Required) Nested block describing the request to use when displaying the widget. Exactly one request block is allowed with the following structure:
            - `x`: (Optional) The query used for the X-Axis. Exactly one nested block is allowed with the following structure:
                - `q`: (Required) The metric query to use in the widget.
                - `aggregator` - (Optional) Aggregator used for the request. One of "avg", "min", "max", "sum", "last".
            - `y`: (Optional) The query used for the Y-Axis. Exactly one nested block is allowed with the following structure:
                - `q`: (Required) The metric query to use in the widget.
                - `aggregator` - (Optional) Aggregator used for the request. One of "avg", "min", "max", "sum", "last".
        - `xaxis`: (Optional) Nested block describing the X-Axis Controls. The structure of this block is described [below](dashboard.html#nested-widget-axis-blocks)
        - `yaxis`: (Optional) Nested block describing the Y-Axis Controls. The structure of this block is described [below](dashboard.html#nested-widget-axis-blocks)
        - `color_by_groups` - (Optional) List of groups used for colors.
        - `title`: (Optional) The title of the widget.
        - `title_size`: (Optional) The size of the widget's title. Default is 16.
        - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
        - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `timeseries_definition`: The definition for a Timeseries  widget. Exactly one nested block is allowed with the following structure:
        - `request`: (Required) Nested block describing the request to use when displaying the widget. Multiple request blocks are allowed with the following structure (exactly only one of `q`, `apm_query`, `log_query` or `process_query` is required within the request block):
            - `q`: (Optional) The metric query to use in the widget
            - `apm_query`: (Optional) The APM query to use in the widget. The structure of this block is described [below](dashboard.html#nested-apm_query-and-log_query-blocks).
            - `log_query`: (Optional) The log query to use in the widget. The structure of this block is described [below](dashboard.html#nested-apm_query-and-log_query-blocks).
            - `process_query`: (Optional) The process query to use in the widget. The structure of this block is described [below](dashboard.html#nested-process_query-blocks).
            - `display_type` - (Optional) Type of display to use for the request. Available values are: `area`, `bars`, or `line`.
            - `style` - (Optional) Style of the widget graph. One nested block is allowed with the following structure:
              - `palette` - (Optional) Color palette to apply to the widget. The available options are available here: https://docs.datadoghq.com/graphing/widgets/timeseries/#appearance.
              - `line_type` - (Optional) Type of lines displayed. Available values are: `dashed`, `dotted`, or `solid`.
              - `line_width` - (Optional) Width of line displayed. Available values are: `normal`, `thick`, or `thin`.
            - `metadata` - (Optional). Used to define expression aliases. Multiple nested blocks are allowed with the following structure:
              - `expression` - (Required)
              - `alias_name` - (Optional)
        - `marker` - (Optional) Nested block describing the marker to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widgetmarker-blocks). Multiple marker blocks are allowed within a given tile_def block.
        - `title`: (Optional) The title of the widget.
        - `title_size`: (Optional) The size of the widget's title. Default is 16.
        - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
        - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `toplist_definition`: The definition for a Toplist  widget. Exactly one nested block is allowed with the following structure:
        - `request`: (Required) Nested block describing the request to use when displaying the widget. Multiple request blocks are allowed with the following structure (exactly only one of `q`, `apm_query`, `log_query` or `process_query` is required within the request block):
            - `q`: (Optional) The metric query to use in the widget
            - `apm_query`: (Optional) The APM query to use in the widget. The structure of this block is described [below](dashboard.html#nested-apm_query-and-log_query-blocks).
            - `log_query`: (Optional) The log query to use in the widget. The structure of this block is described [below](dashboard.html#nested-apm_query-and-log_query-blocks).
            - `process_query`: (Optional) The process query to use in the widget. The structure of this block is described [below](dashboard.html#nested-process_query-blocks).
            - `conditional_formats` - (Optional) Conditional formats allow you to set the color of your widget content or background, depending on a rule applied to your data. Multiple request blocks are allowed. The structure of this block is described [below](dashboard.html#nested-widget-conditional_formats-blocks).
        - `title`: (Optional) The title of the widget.
        - `title_size`: (Optional) The size of the widget's title. Default is 16.
        - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
        - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).
  - `trace_service_definition`: The definition for a Trace Service widget. Exactly one nested block is allowed with the following structure:
        - `env`: (Required) APM environment.
        - `service`: (Required) APM service.
        - `span_name`: (Required) APM span name.
        - `show_hits`: (Optional) APM span name.
        - `show_hits`: (Optional) Whether to show the hits metrics or not.
        - `show_errors`: (Optional) Whether to show the error metrics or not.
        - `show_latency`: (Optional) Whether to show the latency metrics or not.
        - `show_breakdown`: (Optional) Whether to show the latency breakdown or not.
        - `show_distribution`: (Optional) Whether to show the latency distribution or not.
        - `show_resource_list`: (Optional) Whether to show the resource list or not.
        - `size_format`: (Optional) Size of the widget. Available values are: `small`, `medium`, or `large`.
        - `display_format`: (Optional) Number of columns to display. Available values are: `one_column`, `two_column`, or `three_column`.
        - `title`: (Optional) The title of the widget.
        - `title_size`: (Optional) The size of the widget's title. Default is 16.
        - `title_align`: (Optional) The alignment of the widget's title. One of "left", "center", or "right".
        - `time`: (Optional) Nested block describing the timeframe to use when displaying the widget. The structure of this block is described [below](dashboard.html#nested-widget-time-blocks).


### Nested `widget` `layout` blocks
Nested `layout` blocks have the following structure:

- `x` - (Required) The position of the widget on the x (vertical) axis. Should be greater or equal to 0.
- `y` - (Required) The position of the widget on the y (horizontal) axis. Should be greater or equal to 0.
- `width` - (Required) The width of the widget.
- `height` - (Required) The height of the widget.

### Nested `widget` `axis` blocks
Nested `axis` blocks have the following structure:

- `label` - (Optional) The label of the axis to display on the graph.
- `scale` - (Optional) Specifies the scale type. One of "linear", "log", "pow", "sqrt".
- `min` - (Optional) Specify the minimum value to show on y-axis.
- `max` - (Optional) Specify the minimum value to show on y-axis.
- `include_zero` - (Optional) Always include zero or fit the axis to the data range.

### Nested `widget` `conditional_formats` blocks
Nested `conditional_formats` blocks have the following structure:

- `comparator` - (Required) Comparator to apply from: One of `>`, `>=`, `<`, or `<=`.
- `value` - (Required) Value for the comparator.
- `palette` - (Required) Color palette to apply; One of `blue`, `custom_bg`, `custom_image`, `custom_text`, `gray_on_white`, `green`, `green_on_white`, `grey`, `orange`, `red`, `red_on_white`, `white_on_gray`, `white_on_green`, `white_on_red`, `white_on_yellow`, or `yellow_on_white`.
- `custom_bg_color` - (Optional) Color palette to apply to the background, same values available as palette.
- `custom_fg_color` - (Optional) Color palette to apply to the foreground, same values available as palette.
- `image_url` - (Optional) Displays an image as the background.
.

### Nested `widget` `time` blocks
Nested `widget` `time` blocks have the following structure:

- `live_span` - (Required) The timeframe to use when displaying the widget. One of `10m`, `30m`, `1h`, `4h`, `1d`, `2d`, `1w`.

### Nested `apm_query` and `log_query` blocks
Nested `apm_query` and `log_query` blocks have the following structure (Visit the [ Graph Primer](https://docs.datadoghq.com/graphing/) for more information about these values):

  - `index` - (Required)
  - `compute` - (Required). Exactly one nested block is required with the following structure:
    - `aggregation` - (Required)
    - `facet` - (Optional)
    - `interval` - (Optional)
  - `search` - (Optional). One nested block is allowed with the following structure:
    - `query` - (Optional)
  - `group_by` - (Optional). Multiple nested blocks are allowed with the following structure:
    - `facet` - (Optional)
    - `limit` - (Optional)
    - `sort` - (Optional). One nested block is allowed with the following structure:
      - `aggregation` - (Optional)
      - `order` - (Optional)
      - `facet` - (Optional)


### Nested  `process_query` blocks
Nested `process_query` blocks have the following structure (Visit the [ Graph Primer](https://docs.datadoghq.com/graphing/) for more information about these values):

  - `metric` - (Required)
  - `search_by` - (Required)
  - `filter_by` - (Required)
  - `limit` - (Required)


### Nested `widget` `marker` blocks

Only for widgets of type "timeseries".

Nested `widget` `marker` blocks have the following structure:

- `display_type` - (Required) How the marker lines will look. Possible values are {"error", "warning", "info", "ok"} {"dashed", "solid", "bold"}. Example: "error dashed".
- `value` - (Required) Mathematical expression describing the marker. Examples: `y > 1`, `-5 < y < 0`, `y = 19`.
- `label` - (Optional) A label for the line or range.


### Nested `template_variable` blocks

Nested `template_variable` blocks have the following structure:

- `name` - (Required) The variable name. Can be referenced as $name in `graph` `request` `q` query strings.
- `prefix` - (Optional) The tag group. Default: no tag group.
- `default` - (Optional) The default tag. Default: "\*" (match all).

## Import

dashboards can be imported using their  ID, e.g.

```
$ terraform import datadog_dashboard.my_service_dashboard sv7-gyh-kas
```
