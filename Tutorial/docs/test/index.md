# Practical Build My App Test

If you are here it means you are ready to apply all you learned through the multiple tutorials.

You will be given the following elements :

- A Brewery solution with just the simulator
- A webapp link with an empty solution and workspace pre-configured :
    - An empty power bi workspace
    - An ADX database preconfigured with eventh hub
    - A workspace folder in the Organization Azure Storage
- A set of Babylon access tokens

And now it will be your turn to use all you learned to write a full set of run templates, ETL and dashboard around the simulator.


You can follow the set of actions described in the following page, no command or code will be given here, but you can always go back through the formation for any issue/question you have.

## Run Templates

Before getting further you need to define your run templates.

You probably need an ETL that will allow you to take input data and make it as a valid dataset to be used for your simulator.

You also need to define a template that will get the data for your simulator and run it.

??? info "Reminder"
    You will need to make use of csm-orc and maybe CoAL to gain some time.  
    Once you get to a "docker" version of your simulator you will be limited in which folder you can write data to,
    so look for the environment variables you may need to use from the start.

## ADX database

Your run templates can run, but you will need to store some data to display after those.

Start thinking on which data you may want to display and how to display it.

You will need to prepare the data in ADX, either by moving the probes from the ProbeMeasures table 
if you went through event hub or by getting your table ready to receive data if you go using direct to ADX ingestion.

Maybe you could add some functions to allow simple qureies in Power BI later ?

??? info "Reminder"
    Sending data through the event hub requires some configuration to the simulator to add a consumer.  
    If you want faster data read on your dashboard make sure that you only send a minimal ammount of data 
    (no need to send 1GB of data if you want to display a single number)
    The dashboard can have multiple filter choices in the webapp so preparing your data to use one of those may help


## Power BI

You can try some data export to your ADX database from your local environment
to have some data in it to start working on some reports.

You can work on 2 kinds of reports the ones that will be displayed right under your scenarios and the one that are available next to them.

Get at least one scenario and one general report.

??? info "Reminder"
    Once your reports are ready you will need to ublish them to the power bi app.  
    There you will have to configure their connection to use the Oauth2 protocol

## API Configuration

Now that you have most of your pipeline ready you need to get the information needed in the API.

You will need to update your Solution and Workspace to configure your run templates and your web app.

??? info "Reminder"
    All the configuration of the web app is made in the Workspace  
    Any parameter you may want to use has to be defined in your Solution  

## Final result

You now just need to deploy everything, from building your docker image to deploying your power bi report.  
Once everything is done you should be able to run your ETL and scenario from the web app and results should appear in your reports.

