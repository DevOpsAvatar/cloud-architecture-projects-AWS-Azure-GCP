<picture><source media="(prefers-color-scheme: dark)" srcset="https://nextwork.ai/static/nextwork-logo-paper.svg" /><img src="https://nextwork.ai/static/nextwork-logo-leather.svg" alt="NextWork" width="300" /></picture>

# Host a Website on Amazon S3

**Project Link:** [View Project](https://nextwork.ai/projects/70e2d059-31ab-5f67-8c58-f5edbd451140)

**Author:** Dennis Chigozirim

---

## Introducing Today's Project!

### Project overview

In this project, I demonstrate how to host a static website on Amazon S3.

### Tools and concepts

- **Service used:** Amazon S3 (bucket)
- **Key concepts learned:** making an ACL public, uploading files to an S3 bucket, static website hosting, bucket endpoints

### Time, challenges, and wins

This project took me approximately 45 minutes. The most challenging part was deleting the zip file so that only the unzipped folder remained. It was also the most rewarding part, because I learned a lot and this was my first ever AWS project.

### Why I did this project

I chose this project because I wanted to experience first-hand what working with an AWS service feels like.

## How I Set Up an S3 Bucket

### What I did in this step

I opened Amazon S3 and created a bucket, a storage space for my website files.

### How long it took

Creating the bucket took less than 10 minutes.

### Region selection

I used Europe (Stockholm) `eu-north-1`, the default Region.

### Understanding bucket name uniqueness

S3 bucket names are globally unique. A name must be unique across all AWS accounts and all Regions worldwide, not just within your own account or Region.

![How I Set Up an S3 Bucket](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aws-host-a-website-on-s3_ba6d42ad)

## Upload Website Files to S3

### What I did in this step

I downloaded the HTML file that defines the website and a zip file of its images, then uploaded both to my S3 bucket.

### Files I uploaded

- `index.html`
- `Nextwork - Everyone...love_files.zip` (the website's images)

### How the files work together

Both are needed: the HTML file is the page itself, and the images folder supplies the visuals it displays.

![Upload Website Files to S3](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aws-host-a-website-on-s3_a265af88)

## Static Website Hosting on S3

### What I did in this step

I configured my bucket for static website hosting so I could visit a public link to the site.

### Understanding website hosting

Website hosting is what makes a website available to the public on the internet.

### How I enabled website hosting

In the **Properties** tab of my bucket, I enabled static website hosting.

### Access Control Lists (ACLs)

An ACL is a set of rules that decides who can access a resource.

![Static Website Hosting on S3](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aws-host-a-website-on-s3_c22c54c0)

## Bucket Endpoints

### Understanding bucket endpoint URLs

Once static website hosting is enabled, S3 produces a bucket endpoint URL, which is the link to your website on the internet.

### What I saw when I tested the endpoint

The first time I visited the endpoint, I got a **403 Forbidden** error. The HTML and image files were still private.

![Bucket Endpoints](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aws-host-a-website-on-s3_22ce4daf)

## Success!

### What I did in this step

I made my website files publicly accessible so the site would be live.

### How I resolved the 403 error

I fixed the 403 error by making the ACL public.

![Success!](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aws-host-a-website-on-s3_5d4474f9)

---

*Project completed through [NextWork](https://nextwork.ai).*
