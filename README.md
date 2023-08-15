## Serverless CRM

# The idea

This project came from some help I gave to someone creating a bespoke CRM for a small charity - circa 500 - 1000 members perhaps.  They wanted to store
some member details and send emails - en masse and individually I guess.  They'd written a .NET application which required hosting, and the cost of hosting
was proving to be a potential issue.  It became clear that for almost all the time, the app did nothing.  Member changes are so infrequent and email campaigns
so sparse that it could be weeks or months between activity.  However, there was a requirement for several 'users' to be able to perform actions, so just
having a laptop with the software on it wasn't an option.  It needed to be a secure web app.

There are SaaS solutions for this kind of thing, and they probably could have used one of those, but I wondered if there is a purely serverless solution?
One with the following requirements:

  - a database that stored member details
  - a way of selecting members based on certain criteria, for email campaigns (examples might be all UK members, overseas members, everyone, or an individual.)
  - a way of templating an email and sending an email to selected members, and recording the success status, of the campaign and for each member.
  - a way of adding a member to an email campaign after the fact.
  - a static UI and various APIs but not long-running application such as a database server or web server other than what, say, AWS could provide.

The hardest part of this, I think, is replacing the database with various files that represent all the data required, but which has linearized access and no long running server.
I think the solution to this is access to the files is read-only to everyone but there being a queue of additions/updates servicing a SINGLE lambda.

The act of sending emails is a long-running event (potentially) and so perhaps this becames a containerised app, and a step-function approach to trigger fargate?

Various lambdas can be used for CRUD updates for membership, pushing requests to the appropriate queue for the update lambda.

Similarly, an API can be used to create and trigger an email campaign.

Finally, a static S3 website with Cloudfront can use JavaScript to access the APIs.  We can use AWS Cognito for access management, and encrypytion keys to keep data safe.  I think the most sensitive information kept will be an email and address - no credit card details.
