---
author: lime.postback.se
comments: true
date: 2014-02-18 20:04:31+00:00
layout: post
slug: getting-nice-bootstrap-tables
title: Getting nice bootstrap tables
wordpress_id: 34
categories:
- Wordpress
---

To get nice tables from Bootstrap available in Wordpress the following can be done to incorporate them in the a wordpress blog.

Add the css code below to the Theme settings custom css:

```html
/*Table section from bootstrap------------------------------------------------------------------------------------------------- */

table {
max-width: 100%;
}

th {
text-align: left;
}

.table {
width: 100%;
margin-bottom: 20px;
}

.table > thead > tr > th,
.table > tbody > tr > th,
.table > tfoot > tr > th,
.table > thead > tr > td,
.table > tbody > tr > td,
.table > tfoot > tr > td {
padding: 8px;
line-height: 1.428571429;
vertical-align: top;
border-top: 1px solid #ddd;
}

.table > thead > tr > th {
vertical-align: bottom;
border-bottom: 2px solid #ddd;
}

.table > caption + thead > tr:first-child > th,
.table > colgroup + thead > tr:first-child > th,
.table > thead:first-child > tr:first-child > th,
.table > caption + thead > tr:first-child > td,
.table > colgroup + thead > tr:first-child > td,
.table > thead:first-child > tr:first-child > td {
border-top: 0;
}

.table > tbody + tbody {
border-top: 2px solid #ddd;
}

.table .table {
background-color: #fff;
}

.table-condensed > thead > tr > th,
.table-condensed > tbody > tr > th,
.table-condensed > tfoot > tr > th,
.table-condensed > thead > tr > td,
.table-condensed > tbody > tr > td,
.table-condensed > tfoot > tr > td {
padding: 5px;
}

.table-bordered {
border: 1px solid #ddd;
}

.table-bordered > thead > tr > th,
.table-bordered > tbody > tr > th,
.table-bordered > tfoot > tr > th,
.table-bordered > thead > tr > td,
.table-bordered > tbody > tr > td,
.table-bordered > tfoot > tr > td {
border: 1px solid #ddd;
}

.table-bordered > thead > tr > th,
.table-bordered > thead > tr > td {
border-bottom-width: 2px;
}

.table-striped > tbody > tr:nth-child(odd) > td,
.table-striped > tbody > tr:nth-child(odd) > th {
background-color: #eaeaea;
}

.table-hover > tbody > tr:hover > td,
.table-hover > tbody > tr:hover > th {
background-color: #f5f5f5;
}
```


Decorate the table according to Bootstraps reference, at [http://getbootstrap.com/2.3.2/base-css.html#tables](http://getbootstrap.com/2.3.2/base-css.html#tables)`

classes: ```table, table-striped, table-bordered, table-hover, table-condensed
```




Example can be found at the [DNS](http://lime.postback.se/2014/02/18/basic-dns/) post.
