# Fix container for pod restarts

This Dockerfile fix the previous one by keeping the consistency for
pod restarts. That was evoking httpd and dirsrv not restarting.

