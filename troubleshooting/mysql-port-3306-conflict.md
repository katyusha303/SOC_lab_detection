Upon finishing the installation of the XAMPP(stands for cross-platform, apache, MySQL,PHP ,Perl) app and running the different services that are necessary for the running of

the DVWA, I noticed that the MySQL service won't start. After checking the logs and analyzing, it turned out the problem was a port conflict on port 3306 which was already 

being used by the MySQL8.0 database i previously installed on my laptop and forgot about it. 

This was solved by stopping the service via the services windows(accessed by holding Win+R then entering services.msc), which freed port 3306 to be used by the

MySQL service of the XAMPP app.

