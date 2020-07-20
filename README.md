# UST-Cov2-Model
Description of the software used for the following article:

************************************************************************
*                                                                      *
*  Mobility-Guided Modeling of the COVID-19 Pandemic in Metro Manila   *
*                                                                      *
*       Bernhard Egwolf [1,2] and Nicanor Austriaco, O.P. [3,*]        *
*                                                                      *
* [1] Research Center for Natural and Applied Sciences,                *
*     University of Santo Tomas, Manila, Philippines.                  *
* [2] Department of Mathematics and Physics, College of Science,       *
*     University of Santo Tomas, Manila, Philippines.                  *
* [3] Department of Biology, Providence College, Providence,           *
*     Rhode Island, U.S.A.                                             *
* [*] Corresponding Author:                                            *
*     Rev. Nicanor Pier Giorgio Austriaco, O.P., Ph.D., S.T.D.         *
*     Professor of Biology                                             *
*     Providence College                                               *
*     1 Cunningham Square                                              *
*     Providence, RI 02918                                             *
*     U.S.A.                                                           *
*     Phone: +1-401-865-1690                                           *
*     Email: naustria@providence.edu                                   *
*                                                                      *
*     Article accepted for publication in the Philippine Journal       *
*     of Science.                                                      *
*                                                                      *
************************************************************************

1. Origin of the Software

The original Python source code files DELPHI_model.py and DELPHI_utils.py
were downloaded on April 23, 2020, from the GitHub website
https://github.com/COVIDAnalytics/DELPHI. The COVID Analytics team
developed them to make predictions for the COVID Analytics website
https://www.covidanalytics.io/projections. The main program is the
DELPHI_model.py file, whereas DELPHI_utils.py provides functions and
classes for DELPHI_model.py.

2. Input and Output of the Software

The Python source code files in this folder are modified versions of the
original ones. Modifications in the model are discussed in the article.
The following text discusses what kind of input file needs to be provided
to run the program and what the output files contain. The parameters defined
in DELPHI_model.py are for Metro Manila.

2.1 Input File

The modified DELPHI_model.py program reads in an input file
in CSV format from the subdirectory input with the following data (example):
date,day_since100,case_cnt,death_cnt
3/12/2020,-3,38,2
3/13/2020,-2,46,2
3/14/2020,-1,82,4
3/15/2020,0,105,7
3/16/2020,1,107,7
3/17/2020,2,145,8
3/18/2020,3,156,9
3/19/2020,4,169,9
...
Rows with negative values in the day_since100 column are omitted. The column
case_cnt contains the time-series of the total number of COVID-19 cases
reported and the column death_cnt the time-series of the total number of
COVID-19 deaths reported. If the input file is input/InputData_MetroManila.csv
the following lines in DELPHI_model.py will read in this file:
        if os.path.exists(f"input/InputData_MetroManila.csv"):
            totalcases = pd.read_csv(
                f"input/InputData_MetroManila.csv"
            )
If the name of the input file is different these lines in the code need to
be changed accordingly.

2.2 Output Files

The DELPHI_model.py program writes out a file which ends in ...parameters.csv.
This file contains the optimized variables of the model: Infection Rate,
Median Day of Action, Rate of Action, Rate of Death, Mortality Rate, Internal
Parameter 1, and Internal Parameter 2. All data in the other output files is
calculated with these values. The median day of action and the rate of action
define a steep decrease in level of the function gamma(t) at the median day of
action (lock-down). The function gamma(t) describes the government response
(lock-down and lifting of community quarantine). For example, a level of
gamma(t)=1 means no lock-down and a level of gamma(t)=0 means complete
lock-down, where nobody can infect anybody else. The function gamma(t) is
different form the one in the original DELPHI model. It uses cubic splines
to describe transitions between different constant levels. The minimization
algorithm for optimizing the variables of the model is based on random numbers.
That is, if one runs the DELPHI_model.py program twice with the same parameters
and input file the outputs will not be exactly the same.

Another type of output file ends in ...R.csv. Each of these files contains the
time-series of the gamma function gamma(t), of alpha*gamma(t), and of R(t),
that is, the effective reproduction number.

Another type of output file ends in ...since100.csv. Each of these files
contains the predicted time-series for Total Detected, Active, Active
Hospitalized, Cumulative Hospitalized, Total Detected Deaths, and Active
Ventilated.

Output file names containing ...transition14... belong to predictions for a
gamma function with a total transition time for the lifting of the quarantine
of 14 days (transition2=7).

Output file names containing ...transition30... belong to predictions for a
gamma function with a total transition time for the lifting of the quarantine
of 30 days (transition2=15).

Output file names containing ...step... belong to predictions for a gamma
function with a certain increase in level after the lifting of the quarantine.
The number after step indicates by how much the level was increased. The
expression step0 means that the level is not lifted, that is, the quarantine
continues without change. The expression step10 means that the level goes up
to the initial one before the lock-down, that is, without any quarantine.
The median day of the lifting period is defined by the parameter increase_day.
The lifting starts transition2 days (either 7 or 15 days) before increase_day
and ends transition2 days after the increase_day. The transition is modeled by
a cubic spline.

3. Additional Software for Prediction without Quarantine

The program DELPHI_model_nolockdown.py predicts a scenario without quarantine
(no lock-down). It reads in the values of the optimized variables of the model
from the ...parameters.csv file, which is part of the output of a previous
run of the DELPHI_model.py program. That is, one needs to run DELPHI_model.py
first to optimize the variables of the model and then DELPHI_model_nolockdown.py
to generate predictions for a scenario without quarantine based on the same
optimized variables. DELPHI_model_nolockdown.py replaces the function gamma(t)
with a constant value of 1, whereas all other parameters and variables are
unchanged.

