
# Scheduled Job Utilities

A scoped application that contains utility scripts that will let you know if a scheduled data import is ACTUALLY in progress and lets you know the progress the scheduled job is currently at.

One big issue that I run into when dealing with integrations (especially in a customer ServiceNow environment)  is to know if the scheduled data import jobs are actually in progress. Sometimes jobs will complete in an error state or just stay stuck in a "loading" state. And even right after executing a scheduled job the inevitable "how do we know how far along it is" always comes up. 
Sure there are plenty of ways to see this, you can monitor the staging tables to see rows stream in, or you can view the transform history table to get a better understanding of the overall processing rate or everyone's nightmare, the system logs. 
The problem is that you just have to know where everything is, and it takes more than just a few clicks to get there. I created this app so that when you open a scheduled data import record, or you just executed one, you get immediate feedback on which import set is executing, what state its in, and how many rows are being processed.

## How to get
You can fork this branch to import it to you sub-prod instance or your PDI.
There is no setup involved with this application since its fairly plug and play. The only set up that is potentially requires is to scope the client script down to just a subset of scheduled jobs.

## Application Files
The Scheduled Job Utilities application is a scoped app so everything should be very contained within the Scheduled Job Utilities [x_156954_sch_util] scope.
### Script includes

 - ImportSetReport
	 - Model script include representing the generated report that determines if a scheduled data import is running and what its status is.
 - ScheduledImportReportController
	 - Controller Script include used to create a report on a scheduled jobs job status in a way that can be displayed to the user on the client side.
 - ScheduledJobAjaxHelper
	 - GlideAjax script include to be called by the Monitor Scheduled Job Progress client script.

### Client Scripts
 - Monitor Scheduled Job Progress
	 - Executes when the form loads to check to see if the most recent execution of the current scheduled job is in progress. If the scheduled job deemed to be in progress, the client script will execute once ever 10 seconds to refresh the progress status.

### Access Controls
 - ScheduledJobAjaxHelper (execute)
	 - Default ACL for the client callable script include. Requires same roles listed for the Scheduled Job READ ACL.
		 - import_scheduler
		 - import_admin

### System Properties
 - x_156954_sch_util.interrogate_scheduled_job_progress
	 - True/False
	 - Roles: admin
	 - When set to True, the scheduled job progress trackers will do additional checks to determine if a scheduled job may be accidentally in a stale state. For example, stuck in a "loading" state but no transforms are actually executing. (Warning: although extremely rare, when this is set to true and a scheduled job is stale, this process can take up to 30 seconds to process in order for the proper checks to take place.)

