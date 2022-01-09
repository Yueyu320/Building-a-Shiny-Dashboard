Building a Shiny Dashboard
---

<br/>

## Overview

The goal of this assignment is to create a shiny app / dashboard that would allow a user to explore student grades from a central database. We have simplified this somewhat by providing a local copy of the database `data/gradebook.sqlite` and you only need to consider the case where a single concurrent user is interacting with the data. Below are a series of tasks that progressively introduce the requirements of your shiny app, your final app must meet all of the requirements but the tasks are organized to help you work towards the final product by adding features one at a time.

We do not have a specific UI design in mind for this application and you should feel free to construct it in any way that meets the given requirements. We will discuss the structure of the app during class and lab with specific details on each of the tasks. 

For additional clarification or questions please post on the course discussion [page](https://github.com/Sta523-Fa21/Discussions/discussions).

<br/>

## Task 1 - Basic reporting

Your shiny app should connect to the `gradebook` database and allow the user to select a department and a course then click a button to generate a nicely formatted tabular report of students scores for that class. 

### Specific Requirements:
* The minimal amount of data should be transfered between the database and R, i.e. as much processing as possible should occur within the database not R.

* Course selection options should be updated based on the selected Department, courses should be listed in a reasonable order.

* Department selection options should include an All option which then lists courses for all departments.

* Resulting tabular output should be nicely formatted and organized.

* Missing assignments should be highlighted (no score present in the database).

* The table should be generated only when the button is clicked, similarly the database should only be queried when the button is clicked.

* The table should be in wide format with results for one student per row, columns should be ordered in a logical way given the nature of the data, e.g. group assignments of the same type (hw, lab, etc) together in sequential order.

* Bonus points will be considered for well designed inclusion of summary statistics and or visualizations of the grade data.

<br/>

## Task 2 - Calculating final grades

Now add a feature to your shiny app that will allow the user to optionally calculate a final grade for each student in a course via the entry of a weighting scheme for each of the assignments or assignment types in the class. The final grade should be a decimal value between 0 and 1 that is the weighted and scaled average of the assignments. 

For example a course with:
* 4 homeworks worth 10 pts each 
* 2 projects worth 50 pts each 
* 1 exam worth 100 pts 
if we were to apply a weighting of hw 30%, projects 30%, and exam 40% would then have the following formula for a final grade:
```
grade = 0.3 (hw1 + hw2 + hw3 + hw4)/40 + 0.3 (proj1 + proj2)/100 + 0.4 (exam1)/100
```

The user should be able to enter these weights for each assignment type and when generating the report a new column titled `final grade` should be included with the calculated result for each student. The total points available for each assignment is recorded in the `assignments` table of the `gradebook` database.


### Specific Requirements:

* The weight entry inputs should be dynamically created in accordance with the specific assignment types belonging to the selected class, if a different class is selected they should update automatically.

* If the user selects an invalid weight (<0, >1, or total != 1) then a warning should occurwhen  generating a report (within the UI of the app).

* If the user should be able to use a checkbox to determine f the final grade column is calculated, if not checked the report should be generatd without the `final grade` column.

* Any missing assignments should be considered as 0 points.

* Bonus points will be considered for adding the ability to specify letter grade cutoffs and adding a `letter grade` column to the report based on the values in `final grade`.

* Bonus points will be considered for "saving" these settings each time a report is generated - to do this you should create a new database called `settings.sqlite` in `data/` and record the weight values for the current class into a table (the schema is up to you). If a new class is selected the database should be checked, if the class exists the weight values can be restored and if not defaults values used. 

<br/>

## Task 3 - Correcting mistakes

Your final task is to add a feature that will enable the user to make corrections to the database, but doing so in the a way that does not alter the original `gradebook` database. This will be accomplished by adding an additional `corrections.sqlite` database in `data/` which will be used to update or add entries. 

What this means is that for the previous queries used in Task 1 and 2 you must now add additional logic which will check both `gradebook` and `corrections` databases andmerge the two together with preference being given to the data in `corrections`. In order words, if both databases contain an entry for `Colin Rundel`, `hw1` in `Sta 523` then the points recorded in the `corrections` database are what should be used in the report (and for calculating a final grade). This merging is only necessary for the `gradebook` table and not for the `assignments` table. 

### Specific Requirements:

* Your app should also include a method for editing a students score within a class (or adding a score if one is missing). The user should only be able to change the `points` values.

  * Consider using a modal dialog (launched via button click) to avoid crowding your user interface.   Within the modal dialog the class should be fixed but the assignment and student should be selectable and current points value visible and editable.

* Changes should be *inserted* inserted, via a Submit button, into the `corrections` database, if a correction already existed for that class, student, and assignment then it should be overwritten. *Hint* - take a look at the syntax for inserting and updating SQL, this will likely be the most efficient method, do not collect, edit and then rewrite the entire table back to the db.

* Bonus points will be considered for adding interactivity to the report - e.g. clicking on a score in the table allows for editing of that score (if there is a change the report should be regenerated)

<br/>