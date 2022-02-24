---
layout: default
image: geisel
reports:
  - title: Lab Report 1
    path: lab-report-1-week-2
  - title: Lab Report 2
    path: lab-report-2-week-4
  - title: Streamlining SSH configuration
    path: lab-report-3-week-6
  - title: Week 8 lab report
    path: lab-report-4-week-8
---

whatever you like

[Playing with Markdown](https://sheeptester.github.io/cse15l-lab-reports/any%20name%20you%20choose.html)

{% for report in page.reports %}

- **[{{ report.title }}]({{ report.path }}.html)**
- [{{ report.title }}](https://sheeptester.github.io/cse15l-lab-reports/{{ report.path }}.html)
- [{{ report.title }}]({{ report.path }}.md)

{% endfor %}

[GitHub repo](https://github.com/SheepTester/cse15l-lab-reports)
