# SDA1003 - Week 9 Assignment

## AWS Web Application Deployment with ALB + ASG + S3

### Scenario: Clarusway Bootcamp Website Deployment

Deploy the provided `index.html` with logos as a highly available website using:

1. **S3 for static assets**
2. **Auto Scaling Group for NGINX web servers**
3. **Application Load Balancer for traffic distribution**

---

### Part 1: S3 Setup (Static Assets)

1. **Create S3 Bucket:**
   - Name the bucket `SDA1003-clarusway-assets` in the `eu-north-1` region.

2. **Upload the following assets to S3:**
   - `index.html` (provided)
   - `Logo.png`, `sda.png`

3. **Configure the S3 Bucket:**
   - Enable Static Website Hosting.
   - Set the bucket policy to allow public read access:
   
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [{
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::SDA1003-clarusway-assets/*"
       }]
     }
     ```

4. **Deliverables for Part 1:**
   - Screenshot of the S3 website URL.
   - `curl -I` output showing a 200 OK response.

---

### Part 2: Auto Scaling Group (ASG)

1. **Create Launch Template:**
   - Use the following User Data script to set up an NGINX web server:

     ```bash
     #!/bin/bash
     yum update -y
     yum install nginx -y
     systemctl start nginx
     systemctl enable nginx
     aws s3 cp s3://SDA1003-clarusway-assets/index.html /usr/share/nginx/html/
     ```

2. **Configure ASG:**
   - Min: 1, Max: 3, Desired: 2 instances.
   - Enable EC2 and ELB health checks.

3. **Deliverables for Part 2:**
   - Screenshot of 2 running instances.
   - ASG configuration details.

---

### Part 3: Application Load Balancer (ALB)

1. **Create an Internet-Facing ALB:**
   - Set up an HTTP listener on port 80.
   - Configure a target group with health checks on `/`.

2. **Verify the setup:**
   - Access the website using the ALB DNS name.
   - Test round-robin traffic distribution across instances.

3. **Deliverables for Part 3:**
   - Screenshot of the ALB DNS output.
   - `curl` test results showing different instance IDs.

---

### Success Criteria

- Website should be accessible via both:
  1. S3 endpoint (static version).
  2. ALB endpoint (dynamic via ASG).
  
- The ASG should automatically replace terminated instances.
- All assets (HTML + logos) should load correctly.

### Cleanup Reminder

- Delete the S3 bucket.
- Terminate the Auto Scaling Group (this will automatically delete the instances).
- Remove the Application Load Balancer.

---

### Pro Tips

1. Use this command to verify instance distribution:

   ```bash
   for i in {1..5}; do curl -s YOUR_ALB_DNS | grep "hostname"; done
