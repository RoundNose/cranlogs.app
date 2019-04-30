
# The API of the CRAN downloads database

## General remarks

The download count data was provided by the
[RStudio CRAN mirror](http://cran-logs.rstudio.com/).
The data source is updated daily, and cranlogs tries to update hourly.

The output is JSON. The API was inspired by the
[`download-counts` npm package](https://github.com/npm/download-counts),
and adapted to R.

The service also provides [badges for READMEs](#badges).

There is [an official R client for the API, the `cranlogs` package](https://r-hub.github.io/cranlogs/).

This README documents

* [the web API endpoints, data formats, parameters](#web-api-docs)

* [the available badges](#badges)

## Web API docs

### Top downloaded packages `/top/{period}[/count]`

`period` must be one of `last-day`, `last-week` or `last-month`, and `count` is the 
number of packages to show. `count` can be at most 100 currently. Example output for
`http://cranlogs.r-pkg.org/top/last-day/3`

```js
{
  start: "2015-05-01T00:00:00.000Z",
  end: "2015-05-01T00:00:00.000Z",
  downloads: [
    {
      package: "Rcpp",
      downloads: "5010"
    },
    {
      package: "ggplot2",
      downloads: "4329"
    },
    {
      package: "plyr",
      downloads: "4121"
    }
  ]
}
```

### Trending packages last week `/trending`

Treding packages are the ones that were downloaded at least 1000 times during last 
week, and that substantially increased their download counts, compared to the 
average weekly downloads in the previous 24 weeks. The percentage of increase
is also shown in the output:

```js
[
  {
    package: "fpp",
    increase: "914.4002185991438200"
  },
  {
    package: "TSA",
    increase: "764.1159377095596500"
  },
  {
    package: "readr",
    increase: "662.9005975013579600"
  },
...
```


### Total downloads over a period `/downloads/total/{period}[/{package1,package2,...}]`

The output looks like this:

```js
[{
  downloads: 201761,
  start: "2014-06-01",
  end: "2014-06-30",
  package: "ggplot2"
}]
```

If there was no package given, then that key is missing.

#### Examples

All packages, last day:

> [`http://cranlogs.r-pkg.org/downloads/total/last-day`](http://cranlogs.r-pkg.org/downloads/total/last-day)

All packages, specific date:

> [`http://cranlogs.r-pkg.org/downloads/total/2014-02-01`](http://cranlogs.r-pkg.org/downloads/total/2014-02-01)


Package `ggplot2`, last week:

> [`http://cranlogs.r-pkg.org/downloads/total/last-week/ggplot2`](http://cranlogs.r-pkg.org/downloads/total/last-week/ggplot2)


Package `ggplot2`, given 7-day period:

> [`http://cranlogs.r-pkg.org/downloads/total/2014-02-01:2014-02-08/ggplot2`](http://cranlogs.r-pkg.org/downloads/total/2014-02-01:2014-02-08/ggplot2)


Package `ggplot2`, last 30 days:

> [`http://cranlogs.r-pkg.org/downloads/total/last-month/ggplot2`](http://cranlogs.r-pkg.org/downloads/total/last-month/ggplot2)


Package "ggplot2", specific month:

> [`http://cranlogs.r-pkg.org/downloads/total/2014-01-01:2014-01-31/ggplot2`](http://cranlogs.r-pkg.org/downloads/total/2014-01-01:2014-01-31/ggplot2)


Multiple packages at once:

> [`http://cranlogs.r-pkg.org/downloads/total/last-day/igraph,ggplot2,Rcpp`](http://cranlogs.r-pkg.org/downloads/total/last-day/igraph,ggplot2,Rcpp)


#### Accepted values

In general, dates in the `yyyy-mm-dd` ISO 8601 format are
accepted. The following keywords can also be used:

`last-day` Yesterday, unless the database has not been updated yet for yesterday,
in which case it is the day before.

`last-week` Last 7 available days.

`last-month` Last 30 available days.

### Daily downloads `/downloads/daily/{period}[/{package}]`

Output looks like this:

```js
[{
    downloads: [
        {
            day: "2014-09-27",
            downloads: 1581
        },
        ...
        {
            day: "2014-10-04",
            downloads: 2395
        }
    ],
    start: "2014-09-27",
    end: "2014-10-04",
    package: "ggplot2"
}]
```

Again, if no package was specified, then that key is missing.

#### Examples

Downloads per day, last 7 days:

> [`http://cranlogs.r-pkg.org/downloads/daily/last-week`](http://cranlogs.r-pkg.org/downloads/daily/last-week)


Downloads per day, specific 7 days:

> [`http://cranlogs.r-pkg.org/downloads/daily/2014-02-07:2014-02-14`](http://cranlogs.r-pkg.org/downloads/daily/2014-02-07:2014-02-14)


Downloads per day, last 30 days:

> [`http://cranlogs.r-pkg.org/downloads/daily/last-month/ggplot2`](http://cranlogs.r-pkg.org/downloads/daily/last-month/ggplot2)


Downloads per day, specific 30 day period:

> [`http://cranlogs.r-pkg.org/downloads/daily/2014-01-03:2014-02-03/ggplot2`](http://cranlogs.r-pkg.org/downloads/daily/2014-01-03:2014-02-03/ggplot2)

### Downloads of R

If instead of a list of package, `R` is given, downloads of R are
returned. For `total`, the total number of downloads for a given
period. For `daily`, daily downloads, separately for various
operating systems and R versions. Here are some examples:

Last day, separately for R versions and operating systems:

> [`http://cranlogs.r-pkg.org/downloads/daily/last-day/R`](http://cranlogs.r-pkg.org/downloads/daily/last-day/R)

Total number of downloads last week:

> [`http://cranlogs.r-pkg.org/downloads/total/last-week/R`](http://cranlogs.r-pkg.org/downloads/total/last-week/R)


## Badges 

> `/badges[/{summary}]/{package}[?color={color}]`

Returns an SVG badge with download counts. Monthly, weekly and daily
summaries and the total number of downloads are available.
If the summary type is not given, the number of downloads per month is shown:

> `http://cranlogs.r-pkg.org/badges/Rcpp`
>
> ![](http://cranlogs.r-pkg.org/badges/Rcpp)

Weekly downloads are shown with `last-week`:

> `http://cranlogs.r-pkg.org/badges/last-week/Rcpp`
>
> ![](http://cranlogs.r-pkg.org/badges/last-week/Rcpp)

Daily downloads are shown with `last-day`:

> `http://cranlogs.r-pkg.org/badges/last-day/Rcpp`
>
> ![](http://cranlogs.r-pkg.org/badges/last-day/Rcpp)

Total number of downloads since October of 2012, the month the
[RStudio CRAN mirror](http://cran-logs.rstudio.com/) started publishing
logs, with `grand-total`:

> `http://cranlogs.r-pkg.org/badges/grand-total/Rcpp`
>
> ![](http://cranlogs.r-pkg.org/badges/grand-total/Rcpp)

Colors can be customized. Supported color names are:
`brightgreen`, `green`, `yellowgreen`, `yellow`, `orange`,
`red`, `lightgrey`, `blue`. Hex colors are also supported.
The badge is blue by default, as above. Other colors in
the same order, and the hex color `ff69b4`:

> `http://cranlogs.r-pkg.org/badges/Rcpp?color=brightgreen`
>
> ![](http://cranlogs.r-pkg.org/badges/Rcpp?color=brightgreen)
> ![](http://cranlogs.r-pkg.org/badges/Rcpp?color=green)
> ![](http://cranlogs.r-pkg.org/badges/Rcpp?color=yellowgreen)
> ![](http://cranlogs.r-pkg.org/badges/Rcpp?color=yellow)
> ![](http://cranlogs.r-pkg.org/badges/Rcpp?color=orange)
> ![](http://cranlogs.r-pkg.org/badges/Rcpp?color=red)
> ![](http://cranlogs.r-pkg.org/badges/Rcpp?color=lightgrey)
> ![](http://cranlogs.r-pkg.org/badges/Rcpp?color=ff69b4)

Our badges were designed and created by [shields.io](http://shields.io).

