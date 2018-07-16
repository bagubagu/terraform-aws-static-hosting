# Terraform AWS Static Hosting

Create AWS infrastructure resources for cloudfront hosting static website.

## How to use

1. Start from `your-project` directory. Change the value of `aws.profile`
and `hosting.hosted_zone` below.

	``` bash
	cd your-project
	mkdir infra
	cd infra
	
	# create main.tf
	cat << EOF > main.tf
	provider "aws" {
	    region  = "us-east-1"
	    profile = "exampleAdmin"
	}

	module "hosting" {
	    source        = "github.com/bagubagu/terraform-aws-static-hosting"
	    hosted_zone   = "example.com"
	    # uncomment if you want to force delete s3 buckets on destroy
	    # force_destroy = true
	}
	EOF
	```
	
1.  Init and run terraform apply from the `infra` directory

    ```bash
    terraform init
    terraform apply
    ```

1.  Bob's your uncle.

## What does this do?

If hosted_zone is `example_com`:

- Setup s3 originbucket `example-com-origin`
- Setup s3 cloudformation log bucket `example-com-cloudfront-log`
- Add apex and www records to DNS
- Setup cloudfront with alias `example.com` and `www.example.com`
- Configure cloudfront to use lambda@edges
- Create ACM certificate `example.com` and `*.example.com`
- Setup all corresponding IAM users, groups, policies, and roles

## FAQ

### I got InvalidViewerCertificate error

It's a bug. Please re-run `terraform apply` and you should be be fine the second time.

```
Error: Error applying plan:

1 error(s) occurred:

* module.static.aws_cloudfront_distribution.origin: 1 error(s) occurred:

* aws_cloudfront_distribution.origin: error creating CloudFront Distribution: InvalidViewerCertificate: The specified SSL certificate doesn't exist, isn't in us-east-1 region, isn't valid, or doesn't include a valid certificate chain.
    status code: 400, request id: 68fae058-72bc-11e8-a266-5d955149d452
```

## LICENSE

MIT
