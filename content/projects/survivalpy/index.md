+++
date = '2017-02-01T00:00:00'
title = 'SurvivalPy'
tags = ["python", "statistics", "genomics", "science"]
+++

## Summary

SurvivalPy is a python library for computing the intervals that are used to produce Keplan-Meier curves. Keplen-Meier curves are stepwise functions showing the probability of survival/death/disease free. Additionally this library also has the capability to run the log-rank test between two or more curves to compute the statistical significance.

License: GPLv3

Source: https://github.com/andricDu/SurvivalPy

Package: https://pypi.org/project/SurvivalPy

## History

This library was originally used in the NIH Genome Data Commons data portal and was inspired from the original Java implementation I did for ICGC.

Original Java Implementation: https://github.com/icgc-dcc/dcc-portal/blob/develop/dcc-portal-server/src/main/java/org/icgc/dcc/portal/server/analysis/KaplanMeier.java

Usage in GDC: https://portal.gdc.cancer.gov/analysis_page?app=CohortComparisonApp&demoMode=true

Companion OncoJS library for visualization: https://github.com/oncojs/survivalplot

## Example usage

Load data:
~~~python
from survivalpy.survival import Analyzer
from survivalpy.survival import Datum

data = [Datum(1, False, {'id': 'D13'}),
        Datum(1, True, {'id': 'D54'}),
        Datum(3, False, {'id': 'D81'}),
        Datum(4, False, {'id': 'D95'}),
        Datum(6, True, {'id': 'D32'}),
        Datum(6, False, {'id': 'D20'}),
        Datum(9, True, {'id': 'D51'})]


analyzer = Analyzer(data)
results = analyzer.compute()
~~~

Generate the intervals:

~~~python
>>> results = analyzer.compute()
>>> json_results = list(map(lambda interval: interval.to_json_dict(), results))
>>> print(json.dumps(json_results))
[{"start": 0, "donors": [{"time": 1, "censored": false, "meta": {"id": "D13"}}, {"time": 1, "censored": true, "meta": {"id": "D54"}}], "censored": 1, "died": 1, "cumulativeSurvival": 1, "end": 1}, {"start": 0, "donors": [{"time": 3, "censored": false, "meta": {"id": "D81"}}], "censored": 0, "died": 1, "cumulativeSurvival": 0.8333333333333334, "end": 3}, {"start": 0, "donors": [{"time": 4, "censored": false, "meta": {"id": "D95"}}], "censored": 0, "died": 1, "cumulativeSurvival": 0.8, "end": 4}, {"start": 0, "donors": [{"time": 6, "censored": true, "meta": {"id": "D32"}}, {"time": 6, "censored": false, "meta": {"id": "D20"}}], "censored": 1, "died": 1, "cumulativeSurvival": 0.75, "end": 6}, {"start": 0, "donors": [], "censored": 0, "died": 0, "cumulativeSurvival": 0, "end": 6}]
~~~

Run the log-ran test of significance:

~~~python
from survivalpy.logrank import LogRankTest
from survivalpy.survival import Analyzer

...

# Two curves expressed as intervals
curves = [analyzer_1.compute(), analyzer_2.compute()]
stats = LogRankTest(survival_results=curves).compute()
~~~

Output:

~~~json
{"chiSquared": 15.232850289359583, "pValue": 9.503581328784705e-05, "degreesFreedom": 1}
~~~
