## dns-using-service-catalogue-and-control-tower-customizations
The solution uses the Customizations for Control Tower framework and AWS Service Catalog to provision the DNS resources across a multi-account setup. The Service Catalog Portfolio created by the solution consists of three Amazon Route 53 products: Outbound DNS product, Inbound DNS product, and Private DNS. Sharing this portfolio with the organization makes the products available to both existing and future accounts in your organization. Users who are given access to AWS Service Catalog can choose to provision these three Route 53 products in a self-service or a programmatic manner.

1.	Outbound DNS product. This solution creates inbound and outbound Route 53 resolver endpoints in a Networking Hub account. Deploying the solution creates a set of Route 53 resolver rules in the same account. These resolver rules are then shared with the organization via AWS Resource Access Manager (RAM). Amazon VPCs in spoke accounts are then associated with the shared resolver rules by the Service Catalog Outbound DNS product.

2.	Inbound DNS product. A private hosted zone is created in the Networking Hub account to provide on-premises resolution of Amazon VPC IP addresses. A DNS forwarder for the cloud namespace is required to be configured by the customer in the on-premises DNS servers , pointing to the IP addresses of the Route 53 Inbound Resolver endpoints. Appropriate resource records (such as a CNAME record to a spoke account resource like an Elastic Load Balancer or a Private hosted Zone) are added. Once this has been done, the spoke accounts can launch the Inbound DNS Service Catalog product. This activates an AWS Lambda function in the hub account to authorize the spoke VPC to be associated to the Hub account private hosted zone. This should allow a client from on-premises to resolve the IP address of resources in your VPCs in AWS.

3.	Private DNS product. For Private Hosted Zones in the spoke accounts, the corresponding Service Catalog product allows each spoke account to deploy a private hosted zone. The DNS name is a subdomain of the parent domain for your organization. For example, if the parent domain is cloud.example.com, one of the spoke account domains could be called spoke3.cloud.example.com. The product uses the local VPC ID (spoke account) and the Network Hub VPC ID. It also uses the Region for the Network Hub VPC that is associated to this private hosted zone. You provide the ARN of the Amazon SNS topic from the Networking Hub account. This creates an association of the Hub VPC to the newly created private hosted zone which allows the spoke account to notify the Networking Hub account.

The notification from the spoke account is performed via a custom resource that is a part of the Private Hosted Zone product. Processing of the notification in the Networking Hub account to create the VPC association is performed by a Lambda function in the Networking Hub account. We also record each authorization-association within Amazon DynamoDB tables in the Networking Hub account. One table is mapping the account ID with Private Hosted Zone IDs and domain name, and the second table is mapping Hosted Zone Ids with VPC IDs.

## Team
Shiva Vaidyanathan - vaidys@amazon.com
Sr. Cloud Infrastructure Architect

Pratik Jain - pratikdj@amazon.com
Cloud Infrastructure Architect

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

