#htb #Infra-based-enumeration 

In cloud resources we can often start with looking for S3 buckets (AWS), blobs (Azure) or Cloud Storage (GCP).

One of the way to find such resources is via Google Dorking.

### Google Search for AWS

```
intext:company inurl:amazonaws.com

site:amazonaws.com "nw18"
```

### Google Search for Azure

```
intext:company inurl:blob.windows.core.net

site:blob.windows.core.net "nw18"
```

### Target Website - Source Code

We can find multiple file included in the source code of the web pages, from where the images, JS code and CSS are loaded. This is used to relieve multiple resource of the web server and does not store unnecessary content.

### Doman.glass

This can tell us a lot about company's infrastructure.

### GrayHatWarfare

We can discover multiple AWS, Azure and GCP cloud storage.

### Private and Public SSH Keys Leaked