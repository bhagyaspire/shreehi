{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Description": "(SO0010) - Cost Optimization Monitor - AWS CloudFormation Template for AWS Solutions Builder Cost Optimization Tool - Monitor - **WARNING** This template creates AWS resources. You will be billed for the AWS resources used if you create a stack from this template.",
  "Parameters": {
    "KeyName": {
      "Description": "Existing Amazon EC2 key pair for SSH access to the instances to the Nginx proxy server",
      "Type": "AWS::EC2::KeyPair::KeyName",
      "ConstraintDescription": "must be the name of an existing EC2 KeyPair."
    },
    "SSHLocation": {
      "Description": "IP address range that can access the Nginx proxy server",
      "Type": "String",
      "MinLength": "9",
      "MaxLength": "18",
      "AllowedPattern": "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})",
      "ConstraintDescription": "must be a valid IP CIDR range of the form x.x.x.x/x."
    },
    "ElasticsearchDomainName": {
      "Type": "String",
      "Default": "comdomain",
      "Description": "Name for the Amazon ES domain that this template will create. Note: Domain names must start with a lowercase letter and must be between 3 and 28 characters. Valid characters are a-z (lowercase only), 0-9, and – (hyphen)",
      "AllowedPattern": "[a-z][a-z0-9\\-]+"
    },
    "ClusterSize": {
      "Type": "String",
      "Default": "Small",
      "AllowedValues": [
        "Small",
        "Medium",
        "Large"
      ],
      "Description": "Amazon ES cluster size: small, medium, large"
    },
    "DBRBucketInput": {
      "Type": "String",
      "Description": "Select Yes if you want to use an existing S3 bucket to store detailed billing reports",
      "Default": "No",
      "AllowedValues": [
        "Yes",
        "No"
      ]
    },
    "DBRBucketName": {
      "Type": "String",
      "Description": "If you selected Yes for the previous parameter, type the name of existing S3 bucket name that you want to use to store billing reports. If you selected No, leave this field empty"
    },
    "ProxyUsername": {
      "Type": "String",
      "Description": "User name for dashboard access via the proxy server"
    },
    "ProxyPass": {
      "NoEcho": "true",
      "Description": "Password for dashboard access via the proxy server",
      "Type": "String",
      "MinLength": "6",
      "MaxLength": "41",
      "AllowedPattern": "^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z]).{6,}$",
      "ConstraintDescription": "Must contain at least 1 Upper/Lower alphanumeric characters and number (Mininum lenght is 6)"
    },
    "VPCCidrparameter": {
      "Description": "CIDR block for VPC",
      "Type": "String",
      "MinLength": "9",
      "MaxLength": "18",
      "AllowedPattern": "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})",
      "Default": "10.250.0.0/16",
      "ConstraintDescription": "must be a valid IP CIDR range of the form x.x.x.x/x."
    },
    "Subnet1Cidrparameter": {
      "Description": "IP address range for subnet created in AZ1",
      "Type": "String",
      "MinLength": "9",
      "MaxLength": "18",
      "AllowedPattern": "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})",
      "Default": "10.250.250.0/24",
      "ConstraintDescription": "must be a valid IP CIDR range of the form x.x.x.x/x."
    },
    "Subnet2Cidrparameter": {
      "Description": "IP address range for subnet created in AZ2",
      "Type": "String",
      "MinLength": "9",
      "MaxLength": "18",
      "AllowedPattern": "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})",
      "Default": "10.250.251.0/24",
      "ConstraintDescription": "must be a valid IP CIDR range of the form x.x.x.x/x."
    },
    "SendAnonymousData": {
      "Description": "Send anonymous data to AWS",
      "Type": "String",
      "Default": "Yes",
      "AllowedValues": [
        "Yes",
        "No"
      ]
    }
  },
  "Metadata": {
    "AWS::CloudFormation::Interface": {
      "ParameterGroups": [{
          "Label": {
            "default": "Proxy Configuration"
          },
          "Parameters": ["ProxyUsername", "ProxyPass", "SSHLocation", "KeyName"]
        },
        {
          "Label": {
            "default": "Amazon ES Domain Configuration"
          },
          "Parameters": [
            "ElasticsearchDomainName",
            "ClusterSize"
          ]
        },
        {
          "Label": {
            "default": "Amazon S3 Bucket Configuration"
          },
          "Parameters": [
            "DBRBucketInput",
            "DBRBucketName"
          ]
        },
        {
          "Label": {
            "default": "Network Settings"
          },
          "Parameters": ["VPCCidrparameter", "Subnet1Cidrparameter", "Subnet2Cidrparameter"]
        },
        {
          "Label": {
            "default": "Send anonymous data to AWS"
          },
          "Parameters": [
            "SendAnonymousData"
          ]
        }

      ],
      "ParameterLabels": {
        "SendAnonymousData": {
          "default": "Send Anonymous Usage Data"
        },
        "VPCCidrparameter": {
          "default": "VPC CIDR block"
        },
        "Subnet1Cidrparameter": {
          "default": "1st Subnet Network"
        },
        "Subnet2Cidrparameter": {
          "default": "2nd Subnet Network"
        },
        "KeyName": {
          "default": "SSH Key"
        },
        "SSHLocation": {
          "default": "Access CIDR Block"
        },
        "DBRBucketInput": {
          "default": "Use existing bucket?"
        },
        "DBRBucketName": {
          "default": "Existing S3 bucket name"
        },
        "ProxyUsername": {
          "default": "User Name"
        },
        "ProxyPass": {
          "default": "Password"
        },
        "ElasticsearchDomainName": {
          "default": "Domain Name"
        },
        "ClusterSize": {
          "default": "Cluster Size"
        }
      }

    }
  },
  "Conditions": {
    "SendData": {
      "Fn::Equals": [{
          "Ref": "SendAnonymousData"
        },
        "Yes"
      ]
    },
    "UseExistingBucket": {
      "Fn::Equals": [{
          "Ref": "DBRBucketInput"
        },
        "Yes"
      ]
    },
    "UseExistingBucketNot": {
      "Fn::Equals": [{
          "Ref": "DBRBucketInput"
        },
        "No"
      ]
    }

  },
  "Mappings": {
    "AmiMap": {
      "us-west-1": {
        "HVM64": "ami-0019ef04ac50be30f"
      },
      "eu-central-1": {
        "HVM64": "ami-09def150731bdbcc2"
      },
      "cn-north-1": {
        "HVM64": "ami-0cad3dea07a7c36f9"
      },
      "us-east-1": {
        "HVM64": "ami-0de53d8956e8dcf80"
      },
      "ap-northeast-2": {
        "HVM64": "ami-047f7b46bd6dd5d84"
      },
      "us-gov-west-1": {
        "HVM64": "ami-6b157f0a"
      },
      "sa-east-1": {
        "HVM64": "ami-0669a96e355eac82f"
      },
      "ap-northeast-3": {
        "HVM64": "ami-088d713d672ed235e"
      },
      "ap-northeast-1": {
        "HVM64": "ami-0f9ae750e8274075b"
      },
      "ap-southeast-1": {
        "HVM64": "ami-0b419c3a4b01d1859"
      },
      "us-east-2": {
        "HVM64": "ami-02bcbb802e03574ba"
      },
      "ap-southeast-2": {
        "HVM64": "ami-04481c741a0311bbb"
      },
      "cn-northwest-1": {
        "HVM64": "ami-094b7433620966eb5"
      },
      "eu-west-1": {
        "HVM64": "ami-07683a44e80cd32c5"
      },
      "eu-north-1": {
        "HVM64": "ami-d16fe6af"
      },
      "us-gov-east-1": {
        "HVM64": "ami-1208ee63"
      },
      "ap-south-1": {
        "HVM64": "ami-0889b8a448de4fc44"
      },
      "eu-west-3": {
        "HVM64": "ami-0451ae4fd8dd178f7"
      },
      "eu-west-2": {
        "HVM64": "ami-09ead922c1dad67e4"
      },
      "ca-central-1": {
        "HVM64": "ami-03338e1f67dae0168"
      },
      "us-west-2": {
        "HVM64": "ami-061392db613a6357b"
      }
    },
    "ec2instanceSizing": {
      "elasticsearch": {
        "Small": "t2.large",
        "Medium": "m4.large",
        "Large": "m4.xlarge"
      }
    },
    "esRegion2Type": {
      "us-west-1": {
        "type": "elasticsearchm4"
      },
      "eu-central-1": {
        "type": "elasticsearchm4"
      },
      "cn-north-1": {
        "type": "elasticsearchm4"
      },
      "us-east-1": {
        "type": "elasticsearchm4"
      },
      "ap-northeast-2": {
        "type": "elasticsearchm4"
      },
      "us-gov-west-1": {
        "type": "elasticsearchm4"
      },
      "sa-east-1": {
        "type": "elasticsearchm4"
      },
      "ap-northeast-3": {
        "type": "elasticsearchm4"
      },
      "ap-northeast-1": {
        "type": "elasticsearchm4"
      },
      "ap-southeast-1": {
        "type": "elasticsearchm4"
      },
      "us-east-2": {
        "type": "elasticsearchm4"
      },
      "ap-southeast-2": {
        "type": "elasticsearchm4"
      },
      "cn-northwest-1": {
        "type": "elasticsearchm4"
      },
      "eu-west-1": {
        "type": "elasticsearchm4"
      },
      "eu-north-1": {
        "type": "elasticsearchi3"
      },
      "us-gov-east-1": {
        "type": "elasticsearchr4"
      },
      "ap-south-1": {
        "type": "elasticsearchm4"
      },
      "eu-west-3": {
        "type": "elasticsearchr4"
      },
      "eu-west-2": {
        "type": "elasticsearchm4"
      },
      "ca-central-1": {
        "type": "elasticsearchm4"
      },
      "us-west-2": {
        "type": "elasticsearchm4"
      }
    },
    "esinstanceSizing": {
      "elasticsearchm4": {
        "Small": "m4.large.elasticsearch",
        "Medium": "m4.large.elasticsearch",
        "Large": "m4.xlarge.elasticsearch"
      },
      "elasticsearchr4": {
        "Small": "t2.small.elasticsearch",
        "Medium": "t2.small.elasticsearch",
        "Large": "r4.large.elasticsearch"
      },
      "elasticsearchi3": {
        "Small": "i3.large.elasticsearch",
        "Medium": "i3.large.elasticsearch",
        "Large": "i3.large.elasticsearch"
      }
    },
    "esmasterSizing": {
      "elasticsearchm4": {
        "Small": "t2.small.elasticsearch",
        "Medium": "t2.small.elasticsearch",
        "Large": "m4.large.elasticsearch"
      },
      "elasticsearchr4": {
        "Small": "t2.small.elasticsearch",
        "Medium": "t2.small.elasticsearch",
        "Large": "r4.large.elasticsearch"
      },
      "elasticsearchi3": {
        "Small": "i3.large.elasticsearch",
        "Medium": "i3.large.elasticsearch",
        "Large": "i3.large.elasticsearch"
      }
    },
    "instanceCount": {
      "elasticsearch": {
        "Small": "2",
        "Medium": "4",
        "Large": "8"
      }
    },
    "ELBLoggingAccountIDMap": {
      "us-east-1": {
        "AccoundID": "127311923021"
      },
      "us-east-2": {
        "AccoundID": "033677994240"
      },
      "us-west-1": {
        "AccoundID": "027434742980"
      },
      "us-west-2": {
        "AccoundID": "797873946194"
      },
      "ca-central-1": {
        "AccoundID": "985666609251"
      },
      "eu-central-1": {
        "AccoundID": "054676820928"
      },
      "eu-west-1": {
        "AccoundID": "156460612806"
      },
      "eu-west-2": {
        "AccoundID": "652711504416"
      },
      "eu-west-3": {
        "AccoundID": "009996457667"
      },
      "eu-north-1": {
        "AccoundID": "897822967062"
      },
      "ap-northeast-1": {
        "AccoundID": "582318560864"
      },
      "ap-northeast-2": {
        "AccoundID": "600734575887"
      },
      "ap-northeast-3": {
        "AccoundID": "383597477331"
      },
      "ap-southeast-1": {
        "AccoundID": "114774131450"
      },
      "ap-southeast-2": {
        "AccoundID": "783225319266"
      },
      "ap-south-1": {
        "AccoundID": "718504428378"
      },
      "sa-east-1": {
        "AccoundID": "507241528517"
      },
      "us-gov-west-1": {
        "AccoundID": "048591011584"
      },
      "us-gov-east-1": {
        "AccoundID": "190560391635"
      },
      "cn-north-1": {
        "AccoundID": "638102146993"
      },
      "cn-northwest-1": {
        "AccoundID": "037604701340"
      }
    }
  },
  "Resources": {
    "VPC": {
      "Type": "AWS::EC2::VPC",
      "Properties": {
        "CidrBlock": {
          "Ref": "VPCCidrparameter"
        },
        "Tags": [{
          "Key": "Name",
          "Value": "Monitor VPC"
        }]
      }
    },
    "VPCRouteTable": {
      "Type": "AWS::EC2::RouteTable",
      "Properties": {
        "VpcId": {
          "Ref": "VPC"
        },
        "Tags": [{
            "Key": "Network",
            "Value": "Public"
          },
          {
            "Key": "Name",
            "Value": "Monitor VPC"
          }
        ]
      }
    },
    "IGW": {
      "Type": "AWS::EC2::InternetGateway",
      "Properties": {
        "Tags": [{
          "Key": "Name",
          "Value": "Monitor VPC IGW"
        }]
      }
    },
    "IGWToInternet": {
      "Type": "AWS::EC2::VPCGatewayAttachment",
      "Properties": {
        "VpcId": {
          "Ref": "VPC"
        },
        "InternetGatewayId": {
          "Ref": "IGW"
        }
      }
    },
    "VPCPublicRoute": {
      "Type": "AWS::EC2::Route",
      "Properties": {
        "RouteTableId": {
          "Ref": "VPCRouteTable"
        },
        "DestinationCidrBlock": "0.0.0.0/0",
        "GatewayId": {
          "Ref": "IGW"
        }
      }
    },
    "VPCPubSub1": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "VpcId": {
          "Ref": "VPC"
        },
        "CidrBlock": {
          "Ref": "Subnet1Cidrparameter"
        },
        "AvailabilityZone": {
          "Fn::Select": [
            "0",
            {
              "Fn::GetAZs": ""
            }
          ]
        },
        "Tags": [{
            "Key": "Network",
            "Value": "Public"
          },
          {
            "Key": "Name",
            "Value": "Monitor VPC Subnet"
          }
        ]
      }
    },
    "VPCPubSub2": {
      "Type": "AWS::EC2::Subnet",
      "Properties": {
        "VpcId": {
          "Ref": "VPC"
        },
        "CidrBlock": {
          "Ref": "Subnet2Cidrparameter"
        },
        "AvailabilityZone": {
          "Fn::Select": [
            "1",
            {
              "Fn::GetAZs": ""
            }
          ]
        },
        "Tags": [{
            "Key": "Network",
            "Value": "Public"
          },
          {
            "Key": "Name",
            "Value": "Monitor VPC Subnet"
          }
        ]
      }
    },
    "VPCPubSubnet1RouteTableAssociation": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "SubnetId": {
          "Ref": "VPCPubSub1"
        },
        "RouteTableId": {
          "Ref": "VPCRouteTable"
        }
      }
    },
    "VPCPubSubnet2RouteTableAssociation": {
      "Type": "AWS::EC2::SubnetRouteTableAssociation",
      "Properties": {
        "SubnetId": {
          "Ref": "VPCPubSub2"
        },
        "RouteTableId": {
          "Ref": "VPCRouteTable"
        }
      }
    },
    "S3DBRBucket": {
      "Type": "AWS::S3::Bucket",
      "Condition": "UseExistingBucketNot",
      "DeletionPolicy": "Retain",
      "Properties": {
        "AccessControl": "Private",
        "PublicAccessBlockConfiguration": {
          "BlockPublicAcls": true,
          "BlockPublicPolicy": true,
          "IgnorePublicAcls": true,
          "RestrictPublicBuckets": true
        }
      }
    },
    "S3DBRBucketPolicy": {
      "Type": "AWS::S3::BucketPolicy",
      "Condition": "UseExistingBucketNot",
      "Properties": {
        "Bucket": {
          "Ref": "S3DBRBucket"
        },
        "PolicyDocument": {
          "Version": "2012-10-17",
          "Statement": [{
              "Effect": "Allow",
              "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
              ],
              "Principal": {
                "AWS": "386209384616"
              },
              "Resource": {
                "Fn::Sub": "arn:aws:s3:::${S3DBRBucket}"
              }
            },
            {
              "Effect": "Allow",
              "Action": "s3:PutObject",
              "Principal": {
                "AWS": "386209384616"
              },
              "Resource": {
                "Fn::Sub": "arn:aws:s3:::${S3DBRBucket}/*"
              }
            }
          ]
        }
      }
    },
    "ELBSecurityGroup": {
      "Type": "AWS::EC2::SecurityGroup",
      "Properties": {
        "GroupDescription": "ELB - Port 80 access",
        "VpcId": {
          "Ref": "VPC"
        },
        "SecurityGroupIngress": [{
          "IpProtocol": "tcp",
          "FromPort": 80,
          "ToPort": 80,
          "CidrIp": {
            "Ref": "SSHLocation"
          }
        }],
        "SecurityGroupEgress": [{
          "IpProtocol": "-1",
          "FromPort": -1,
          "ToPort": -1,
          "CidrIp": "0.0.0.0/0"
        }]
      }
    },
    "S3LoggingBucket": {
      "Type": "AWS::S3::Bucket",
      "DeletionPolicy": "Retain",
      "Properties": {
        "AccessControl": "Private",
        "PublicAccessBlockConfiguration": {
          "BlockPublicAcls": true,
          "BlockPublicPolicy": true,
          "IgnorePublicAcls": true,
          "RestrictPublicBuckets": true
        }
      }
    },
    "S3LoggingBucketPolicy": {
      "Type": "AWS::S3::BucketPolicy",
      "Properties": {
        "Bucket": {
          "Ref": "S3LoggingBucket"
        },
        "PolicyDocument": {
          "Version": "2012-10-17",
          "Statement": [{
            "Effect": "Allow",
            "Action": [
              "s3:PutObject"
            ],
            "Resource": {
              "Fn::Sub": "arn:aws:s3:::${S3LoggingBucket}/Logs/AWSLogs/${AWS::AccountId}/*"
            },
            "Principal": {
              "AWS": [{
                "Fn::FindInMap": ["ELBLoggingAccountIDMap", {
                  "Ref": "AWS::Region"
                }, "AccoundID"]
              }]
            }
          }]
        }
      }
    },
    "ELB": {
      "Type": "AWS::ElasticLoadBalancing::LoadBalancer",
      "DependsOn": "S3LoggingBucketPolicy",
      "Properties": {
        "SecurityGroups": [{
          "Fn::GetAtt": ["ELBSecurityGroup", "GroupId"]
        }],
        "Subnets": [{
          "Ref": "VPCPubSub1"
        }, {
          "Ref": "VPCPubSub2"
        }],
        "CrossZone": true,
        "Instances": [{
          "Ref": "ProxyServer1"
        }, {
          "Ref": "ProxyServer2"
        }],
        "LoadBalancerName": {
          "Ref": "AWS::StackName"
        },
        "Listeners": [{
          "LoadBalancerPort": "80",
          "InstancePort": "80",
          "Protocol": "HTTP"
        }],
        "HealthCheck": {
          "Target": "TCP:80",
          "HealthyThreshold": "3",
          "UnhealthyThreshold": "5",
          "Interval": "30",
          "Timeout": "5"
        },
        "AccessLoggingPolicy": {
          "S3BucketName": {
            "Ref": "S3LoggingBucket"
          },
          "S3BucketPrefix": "Logs",
          "Enabled": true,
          "EmitInterval": 60
        }
      }
    },
    "ProxyServerRole": {
      "Type": "AWS::IAM::Role",
      "Properties": {
        "AssumeRolePolicyDocument": {
          "Version": "2012-10-17",
          "Statement": [{
            "Effect": "Allow",
            "Principal": {
              "Service": [
                "ec2.amazonaws.com"
              ]
            },
            "Action": [
              "sts:AssumeRole"
            ]
          }]
        },
        "Path": "/",
        "Policies": [{
          "PolicyName": "ProxyServerRole",
          "PolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [{
              "Effect": "Allow",
              "Action": [
                "s3:GetObject"
              ],
              "Resource": {
                "Fn::Sub": [
                  "arn:aws:s3:::${BucketName}/*",
                  {
                    "BucketName": {
                      "Fn::If": ["UseExistingBucket", {
                        "Ref": "DBRBucketName"
                      }, {
                        "Ref": "S3DBRBucket"
                      }]
                    }
                  }
                ]
              }
            }]
          }
        }]
      }
    },
    "ProxyServerInstanceProfile": {
      "Type": "AWS::IAM::InstanceProfile",
      "Properties": {
        "Path": "/",
        "Roles": [{
          "Ref": "ProxyServerRole"
        }]
      }
    },
    "ProxyServerSecurityGroup": {
      "Type": "AWS::EC2::SecurityGroup",
      "Properties": {
        "GroupDescription": "Enable HTTP access via port 80 locked down to the load balancer + SSH access",
        "VpcId": {
          "Ref": "VPC"
        },
        "SecurityGroupIngress": [{
            "IpProtocol": "tcp",
            "FromPort": 80,
            "ToPort": 80,
            "CidrIp": {
              "Ref": "SSHLocation"
            }
          },
          {
            "IpProtocol": "tcp",
            "FromPort": 22,
            "ToPort": 22,
            "CidrIp": {
              "Ref": "SSHLocation"
            }
          },
          {
            "IpProtocol": "tcp",
            "FromPort": 80,
            "ToPort": 80,
            "SourceSecurityGroupId": {
              "Fn::GetAtt": ["ELBSecurityGroup", "GroupId"]
            }
          }
        ],
        "SecurityGroupEgress": [{
          "IpProtocol": "-1",
          "FromPort": -1,
          "ToPort": -1,
          "CidrIp": "0.0.0.0/0"
        }]
      }
    },
    "ProxyServer1EIP": {
      "DependsOn": "VPCPubSubnet1RouteTableAssociation",
      "Type": "AWS::EC2::EIP",
      "Properties": {
        "Domain": "vpc"
      }
    },
    "ProxyServer1EIPAssoc": {
      "Type": "AWS::EC2::EIPAssociation",
      "Properties": {
        "AllocationId": {
          "Fn::GetAtt": ["ProxyServer1EIP", "AllocationId"]
        },
        "InstanceId": {
          "Ref": "ProxyServer1"
        }
      }
    },
    "ProxyServer1": {
      "Type": "AWS::EC2::Instance",
      "DependsOn": "VPCPubSubnet1RouteTableAssociation",
      "Metadata": {
        "AWS::CloudFormation::Init": {
          "configSets": {
            "Monitor_install": [
              "install_cfn",
              "install_nginx",
              "configure_nginx",
              "install_awsdbrparser",
              "install_estool",
              "populate_es"
            ]
          },
          "install_cfn": {
            "files": {
              "/etc/cfn/cfn-hup.conf": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "[main]\n",
                      "stack=",
                      {
                        "Ref": "AWS::StackId"
                      },
                      "\n",
                      "region=",
                      {
                        "Ref": "AWS::Region"
                      },
                      "\n"
                    ]
                  ]
                },
                "mode": "000400",
                "owner": "root",
                "group": "root"
              },
              "/etc/cfn/hooks.d/cfn-auto-reloader.conf": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "[cfn-auto-reloader-hook]\n",
                      "triggers=post.update\n",
                      "path=Resources.ProxyServer1.Metadata.AWS::CloudFormation::Init\n",
                      "action=/opt/aws/bin/cfn-init -v ",
                      "         --stack ",
                      {
                        "Ref": "AWS::StackName"
                      },
                      "         --resource ProxyServer1 ",
                      "         --configsets Monitor_install ",
                      "         --region ",
                      {
                        "Ref": "AWS::Region"
                      },
                      "\n"
                    ]
                  ]
                },
                "mode": "000400",
                "owner": "root",
                "group": "root"
              }
            },
            "services": {
              "sysvinit": {
                "cfn-hup": {
                  "enabled": "true",
                  "ensureRunning": "true",
                  "files": [
                    "/etc/cfn/cfn-hup.conf",
                    "/etc/cfn/hooks.d/cfn-auto-reloader.conf"
                  ]
                }
              }
            }
          },
          "install_nginx": {
            "packages": {
              "yum": {
                "git": []
              }
            },
            "commands": {
              "0-setup": {
                "command": "amazon-linux-extras install nginx1.12",
                "cwd": "/home/ec2-user"
              }
            },
            "files": {
              "/tmp/crontab": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "#Content to include in the user crontab.\n",
                      "#You can just create the file first and then copy to the crontab folder\n",
                      "\n",
                      "#Without this path the user wont find the dbrparser program\n",
                      "PATH=/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/aws/bin:/home/dbrparser/.local/bin:/home/dbrparser/bin\n",
                      "\n",
                      "# m h dom mon dow usercommand\n",
                      "00 23 * * * /home/dbrparser/dbr-job/job.sh >>/home/dbrparser/dbr-job/job.log\n",
                      "30 23 1 * * /home/dbrparser/dbr-job/job.sh -pm >>/home/dbrparser/dbr-job/job.log\n"
                    ]
                  ]
                },
                "mode": "000400",
                "owner": "root",
                "group": "root"
              },
              "/tmp/setup.dbr": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "adduser dbrparser\n",
                      "mkdir /home/dbrparser/dbr-job\n",
                      "cp /tmp/crontab /home/dbrparser/crontab\n",
                      "cp /home/ec2-user/aws-detailed-billing-parser-0.6.0/job.sh /home/dbrparser/dbr-job\n",
                      "sed -i 's/bucket-123456/",
                      {
                        "Fn::If": [
                          "UseExistingBucket",
                          {
                            "Ref": "DBRBucketName"
                          },
                          {
                            "Fn::GetAtt": [
                              "S3DBRBucket",
                              "DomainName"
                            ]
                          }
                        ]
                      },
                      "/g' /home/dbrparser/dbr-job/job.sh\n",
                      "sed -i 's:.s3.amazonaws.com::g' /home/dbrparser/dbr-job/job.sh\n",
                      "sed -i 's/123456789012/",
                      {
                        "Ref": "AWS::AccountId"
                      },
                      "/g' /home/dbrparser/dbr-job/job.sh\n",
                      "sed -i 's#/mnt/jobs#/home/dbrparser/dbr-job#g' /home/dbrparser/dbr-job/job.sh\n",
                      "sed -i 's/elastic-search-host.endopoint.name/",
                      {
                        "Fn::GetAtt": [
                          "MyElasticsearchDomain",
                          "DomainEndpoint"
                        ]
                      },
                      "/g' /home/dbrparser/dbr-job/job.sh\n",
                      "chown -R dbrparser:dbrparser /home/dbrparser/dbr-job\n",
                      "chmod +x /home/dbrparser/dbr-job/job.sh\n",
                      "#try the initial import with awsauth option\n",
                      "# the initial import still doesn't work but leaving this code in for future reference\n",
                      "cp /home/dbrparser/dbr-job/job.sh /home/dbrparser/dbr-job/jobawsauth.sh\n",
                      "sed -i 's#--delete-index –bi#--delete-index –bi --awsauth#g' /home/dbrparser/dbr-job/jobawsauth.sh\n",
                      "crontab -u dbrparser /home/dbrparser/crontab\n",
                      "\n"
                    ]
                  ]
                },
                "mode": "000700",
                "owner": "root",
                "group": "root"
              },
              "/tmp/setup.nginx": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "#!/bin/bash\n",
                      "yum update -y\n",
                      "sed -i 's/elastic-search-host.endopoint.name/",
                      {
                        "Fn::GetAtt": [
                          "MyElasticsearchDomain",
                          "DomainEndpoint"
                        ]
                      },
                      "/g' /etc/nginx/default.d/default.conf\n",
                      "nameserver=$(cat /etc/resolv.conf | grep -m1 nameserver | cut -d ' ' -f 2)\n",
                      "sed -i 's/resolver-ip/'$nameserver'/g' /etc/nginx/default.d/default.conf\n"
                    ]
                  ]
                },
                "mode": "000700",
                "owner": "root",
                "group": "root"
              },
              "/tmp/es.sh": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "#!/bin/bash -v\n",
                      "\n",
                      "PATH=/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/aws/bin:/home/ec2-user/.local/bin:/home/ec2-user/bin\n",
                      "es-import -r ",
                      {
                        "Ref": "AWS::Region"
                      },
                      " -e ",
                      {
                        "Fn::GetAtt": [
                          "MyElasticsearchDomain",
                          "DomainEndpoint"
                        ]
                      },
                      " -p 80 -f /tmp/export.json"
                    ]
                  ]
                },
                "mode": "000700",
                "owner": "root",
                "group": "root"
              },
              "/etc/nginx/default.d/default.conf": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "location / {\n",
                      "   resolver resolver-ip;\n",
                      "   set $es ",
                      {
                        "Fn::GetAtt": [
                          "MyElasticsearchDomain",
                          "DomainEndpoint"
                        ]
                      },
                      ";\n",
                      "   auth_basic 'Restricted';\n",
                      "   auth_basic_user_file /etc/nginx/conf.d/kibana.htpasswd;\n",
                      "   proxy_pass_request_headers off;\n",
                      "   proxy_set_header Host $host;\n",
                      "   proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;\n",
                      "   proxy_pass http://$es;",
                      "}\n"
                    ]
                  ]
                },
                "mode": "000644",
                "owner": "root",
                "group": "root"
              }
            },
            "services": {
              "sysvinit": {
                "nginx": {
                  "enabled": "true"
                }
              }
            }
          },
          "configure_nginx": {
            "commands": {
              "0-setup": {
                "command": "/tmp/setup.nginx",
                "cwd": "/home/ec2-user"
              }
            }
          },
          "install_awsdbrparser": {
            "commands": {
              "0-pip": {
                "command": "yum install python-pip -y",
                "cwd": "/home/ec2-user"
              },
              "1-wget_awsdbrparser": {
                "command": "wget https://github.com/awslabs/aws-detailed-billing-parser/archive/0.6.0.tar.gz",
                "cwd": "/home/ec2-user"
              },
              "2-unzip_awsdbrparser": {
                "command": "tar xvzf 0.6.0.tar.gz",
                "cwd": "/home/ec2-user"
              },
              "3-upgrade_urllib3_botocore": {
                "command": "pip install --upgrade urllib3 botocore",
                "cwd": "/home/ec2-user/aws-detailed-billing-parser-0.6.0"
              },
              "4-refresh_awscli": {
                "command": "pip install awscli",
                "cwd": "/home/ec2-user/aws-detailed-billing-parser-0.6.0"
              },
              "5-python_setup": {
                "command": "python setup.py install",
                "cwd": "/home/ec2-user/aws-detailed-billing-parser-0.6.0"
              },
              "6-dbr_setup": {
                "command": "/tmp/setup.dbr",
                "cwd": "/home/ec2-user"
              }
            }
          },
          "install_estool": {
            "commands": {
              "0-git_clone": {
                "command": {
                  "Fn::Sub": "wget https://s3.amazonaws.com/%%TEMPLATE_BUCKET_NAME%%/cost-optimization-monitor/%%VERSION%%/es_tools-0.1.4.tar.gz"
                },
                "cwd": "/home/ec2-user"
              },
              "1-python_setup": {
                "command": "pip install es_tools-0.1.4.tar.gz",
                "cwd": "/home/ec2-user"
              },
              "2-kibana_index": {
                "command": {
                  "Fn::Sub": "wget https://s3.amazonaws.com/%%TEMPLATE_BUCKET_NAME%%/cost-optimization-monitor/%%VERSION%%/export.json"
                },
                "cwd": "/tmp/"
              },
              "3-import_dashboard": {
                "command": "sh /tmp/es.sh"
              }
            }
          },
          "populate_es": {
            "commands": {
              "0-run_dbrparser": {
                "command": "su - dbrparser -c '/home/dbrparser/dbr-job/jobawsauth.sh >>/home/dbrparser/dbr-job/jobawsauth.log'",
                "cwd": "/home/dbrparser/dbr-job"
              }
            }
          }
        }
      },
      "Properties": {
        "ImageId": {
          "Fn::FindInMap": ["AmiMap", {
            "Ref": "AWS::Region"
          }, "HVM64"]
        },
        "InstanceType": {
          "Fn::FindInMap": [
            "ec2instanceSizing",
            "elasticsearch",
            {
              "Ref": "ClusterSize"
            }
          ]
        },
        "IamInstanceProfile": {
          "Ref": "ProxyServerInstanceProfile"
        },
        "NetworkInterfaces": [{
          "AssociatePublicIpAddress": true,
          "DeviceIndex": "0",
          "GroupSet": [{
            "Ref": "ProxyServerSecurityGroup"
          }],
          "SubnetId": {
            "Ref": "VPCPubSub1"
          }
        }],
        "KeyName": {
          "Ref": "KeyName"
        },
        "UserData": {
          "Fn::Base64": {
            "Fn::Join": [
              "",
              [
                "#!/bin/bash -xe\n",
                "yum update -y aws-cfn-bootstrap\n",
                "/opt/aws/bin/cfn-init -v ",
                "         --stack ",
                {
                  "Ref": "AWS::StackName"
                },
                "         --resource ProxyServer1 ",
                "         --configsets Monitor_install ",
                "         --region ",
                {
                  "Ref": "AWS::Region"
                },
                "\n",
                "/opt/aws/bin/cfn-signal -e $? ",
                "         --stack ",
                {
                  "Ref": "AWS::StackName"
                },
                "         --resource ProxyServer1 ",
                "         --region ",
                {
                  "Ref": "AWS::Region"
                },
                "\n",
                "# Create a new username/password for nginx\n",
                "printf ",
                {
                  "Ref": "ProxyUsername"
                },
                ":`openssl passwd -apr1 ",
                {
                  "Ref": "ProxyPass"
                },
                "` >> /etc/nginx/conf.d/kibana.htpasswd\n",
                "# Remove the default location from nginx config\n",
                "sed -ri '/location \\//,/.*\\}/d' /etc/nginx/nginx.conf\n",
                "service nginx restart"
              ]
            ]
          }
        }
      },
      "CreationPolicy": {
        "ResourceSignal": {
          "Timeout": "PT15M"
        }
      }
    },
    "ProxyServer1Alarm": {
      "Type": "AWS::CloudWatch::Alarm",
      "Properties": {
        "AlarmDescription": "Trigger a recovery when instance status check fails for 15 consecutive minutes.",
        "Namespace": "AWS/EC2",
        "MetricName": "StatusCheckFailed_System",
        "Statistic": "Minimum",
        "Period": 60,
        "EvaluationPeriods": 15,
        "ComparisonOperator": "GreaterThanThreshold",
        "Threshold": 0,
        "AlarmActions": [{
          "Fn::Join": ["", ["arn:aws:automate:", {
            "Ref": "AWS::Region"
          }, ":ec2:recover"]]
        }],
        "Dimensions": [{
          "Name": "InstanceId",
          "Value": {
            "Ref": "ProxyServer1"
          }
        }]
      }
    },
    "ProxyServer2EIP": {
      "DependsOn": "VPCPubSubnet2RouteTableAssociation",
      "Type": "AWS::EC2::EIP",
      "Properties": {
        "Domain": "vpc"
      }
    },
    "ProxyServer2EIPAssoc": {
      "Type": "AWS::EC2::EIPAssociation",
      "Properties": {
        "AllocationId": {
          "Fn::GetAtt": ["ProxyServer2EIP", "AllocationId"]
        },
        "InstanceId": {
          "Ref": "ProxyServer2"
        }
      }
    },
    "ProxyServer2": {
      "Type": "AWS::EC2::Instance",
      "DependsOn": "VPCPubSubnet2RouteTableAssociation",
      "Metadata": {
        "AWS::CloudFormation::Init": {
          "configSets": {
            "Monitor_install": [
              "install_cfn",
              "install_nginx",
              "configure_nginx"
            ]
          },
          "install_cfn": {
            "files": {
              "/etc/cfn/cfn-hup.conf": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "[main]\n",
                      "stack=",
                      {
                        "Ref": "AWS::StackId"
                      },
                      "\n",
                      "region=",
                      {
                        "Ref": "AWS::Region"
                      },
                      "\n"
                    ]
                  ]
                },
                "mode": "000400",
                "owner": "root",
                "group": "root"
              },
              "/etc/cfn/hooks.d/cfn-auto-reloader.conf": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "[cfn-auto-reloader-hook]\n",
                      "triggers=post.update\n",
                      "path=Resources.ProxyServer2.Metadata.AWS::CloudFormation::Init\n",
                      "action=/opt/aws/bin/cfn-init -v ",
                      "         --stack ",
                      {
                        "Ref": "AWS::StackName"
                      },
                      "         --resource ProxyServer2 ",
                      "         --configsets Monitor_install ",
                      "         --region ",
                      {
                        "Ref": "AWS::Region"
                      },
                      "\n"
                    ]
                  ]
                },
                "mode": "000400",
                "owner": "root",
                "group": "root"
              }
            },
            "services": {
              "sysvinit": {
                "cfn-hup": {
                  "enabled": "true",
                  "ensureRunning": "true",
                  "files": [
                    "/etc/cfn/cfn-hup.conf",
                    "/etc/cfn/hooks.d/cfn-auto-reloader.conf"
                  ]
                }
              }
            }
          },
          "install_nginx": {
            "packages": {
              "yum": {
                "git": []
              }
            },
            "commands": {
              "0-setup": {
                "command": "amazon-linux-extras install nginx1.12",
                "cwd": "/home/ec2-user"
              }
            },
            "files": {
              "/tmp/setup.nginx": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "sed -i 's/elastic-search-host.endopoint.name/",
                      {
                        "Fn::GetAtt": [
                          "MyElasticsearchDomain",
                          "DomainEndpoint"
                        ]
                      },
                      "/g' /etc/nginx/default.d/default.conf\n",
                      "nameserver=$(cat /etc/resolv.conf | grep -m1 nameserver | cut -d ' ' -f 2)\n",
                      "sed -i 's/resolver-ip/'$nameserver'/g' /etc/nginx/default.d/default.conf\n"
                    ]
                  ]
                },
                "mode": "000700",
                "owner": "root",
                "group": "root"
              },
              "/etc/nginx/default.d/default.conf": {
                "content": {
                  "Fn::Join": [
                    "",
                    [
                      "location / {\n",
                      "   resolver resolver-ip;\n",
                      "   set $es ",
                      {
                        "Fn::GetAtt": [
                          "MyElasticsearchDomain",
                          "DomainEndpoint"
                        ]
                      },
                      ";\n",
                      "   auth_basic 'Restricted';\n",
                      "   auth_basic_user_file /etc/nginx/conf.d/kibana.htpasswd;\n",
                      "   proxy_http_version 1.1;\n",
                      "   proxy_set_header Authorization \"\";\n",
                      "   proxy_set_header Upgrade $http_upgrade;\n",
                      "   proxy_set_header Connection 'upgrade';\n",
                      "   proxy_set_header Host $host;\n",
                      "   proxy_cache_bypass $http_upgrade;\n",
                      "   proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;\n",
                      "   proxy_pass http://$es;",
                      "}\n"
                    ]
                  ]
                },
                "mode": "000644",
                "owner": "root",
                "group": "root"
              }
            },
            "services": {
              "sysvinit": {
                "nginx": {
                  "enabled": "true"
                }
              }
            }
          },
          "configure_nginx": {
            "commands": {
              "0-setup": {
                "command": "/tmp/setup.nginx",
                "cwd": "/home/ec2-user"
              }
            }
          }
        }
      },
      "Properties": {
        "ImageId": {
          "Fn::FindInMap": ["AmiMap", {
            "Ref": "AWS::Region"
          }, "HVM64"]
        },
        "InstanceType": {
          "Fn::FindInMap": [
            "ec2instanceSizing",
            "elasticsearch",
            {
              "Ref": "ClusterSize"
            }
          ]
        },
        "IamInstanceProfile": {
          "Ref": "ProxyServerInstanceProfile"
        },
        "NetworkInterfaces": [{
          "AssociatePublicIpAddress": true,
          "DeviceIndex": "0",
          "GroupSet": [{
            "Ref": "ProxyServerSecurityGroup"
          }],
          "SubnetId": {
            "Ref": "VPCPubSub2"
          }
        }],
        "KeyName": {
          "Ref": "KeyName"
        },
        "UserData": {
          "Fn::Base64": {
            "Fn::Join": [
              "",
              [
                "#!/bin/bash -xe\n",
                "yum update -y aws-cfn-bootstrap\n",
                "/opt/aws/bin/cfn-init -v ",
                "         --stack ",
                {
                  "Ref": "AWS::StackName"
                },
                "         --resource ProxyServer2 ",
                "         --configsets Monitor_install ",
                "         --region ",
                {
                  "Ref": "AWS::Region"
                },
                "\n",
                "/opt/aws/bin/cfn-signal -e $? ",
                "         --stack ",
                {
                  "Ref": "AWS::StackName"
                },
                "         --resource ProxyServer2 ",
                "         --region ",
                {
                  "Ref": "AWS::Region"
                },
                "\n",
                "# Create a new username/password for nginx\n",
                "printf ",
                {
                  "Ref": "ProxyUsername"
                },
                ":`openssl passwd -apr1 ",
                {
                  "Ref": "ProxyPass"
                },
                "` >> /etc/nginx/conf.d/kibana.htpasswd\n",
                "# Remove the default location from nginx config\n",
                "sed -ri '/location \\//,/.*\\}/d' /etc/nginx/nginx.conf\n",
                "service nginx restart"
              ]
            ]
          }
        }
      },
      "CreationPolicy": {
        "ResourceSignal": {
          "Timeout": "PT15M"
        }
      }
    },
    "ProxyServer2Alarm": {
      "Type": "AWS::CloudWatch::Alarm",
      "Properties": {
        "AlarmDescription": "Trigger a recovery when instance status check fails for 15 consecutive minutes.",
        "Namespace": "AWS/EC2",
        "MetricName": "StatusCheckFailed_System",
        "Statistic": "Minimum",
        "Period": 60,
        "EvaluationPeriods": 15,
        "ComparisonOperator": "GreaterThanThreshold",
        "Threshold": 0,
        "AlarmActions": [{
          "Fn::Join": ["", ["arn:aws:automate:", {
            "Ref": "AWS::Region"
          }, ":ec2:recover"]]
        }],
        "Dimensions": [{
          "Name": "InstanceId",
          "Value": {
            "Ref": "ProxyServer2"
          }
        }]
      }
    },
    "MyElasticsearchDomain": {
      "Type": "AWS::Elasticsearch::Domain",
      "Properties": {
        "DomainName": {
          "Ref": "ElasticsearchDomainName"
        },
        "ElasticsearchVersion": "2.3",
        "EBSOptions": {
          "EBSEnabled": true,
          "VolumeType": "gp2",
          "VolumeSize": 20
        },
        "AccessPolicies": {
          "Version": "2012-10-17",
          "Statement": [{
              "Sid": "IP restriction",
              "Action": "es:*",
              "Principal": {
                "AWS": "*"
              },
              "Effect": "Allow",
              "Resource": {
                "Fn::Join": [
                  "",
                  [
                    "arn:aws:es:",
                    {
                      "Ref": "AWS::Region"
                    },
                    ":",
                    {
                      "Ref": "AWS::AccountId"
                    },
                    ":domain/",
                    {
                      "Ref": "ElasticsearchDomainName"
                    },
                    "/*"
                  ]
                ]
              },
              "Condition": {
                "IpAddress": {
                  "aws:SourceIp": [{
                    "Ref": "ProxyServer1EIP"
                  }, {
                    "Ref": "ProxyServer2EIP"
                  }]
                }
              }
            },
            {
              "Sid": "EC2-Role restriction",
              "Action": "es:*",
              "Principal": {
                "AWS": {
                  "Fn::Join": [
                    "",
                    [{
                      "Fn::GetAtt": [
                        "ProxyServerRole",
                        "Arn"
                      ]
                    }]
                  ]
                }
              },
              "Effect": "Allow",
              "Resource": {
                "Fn::Join": [
                  "",
                  [
                    "arn:aws:es:",
                    {
                      "Ref": "AWS::Region"
                    },
                    ":",
                    {
                      "Ref": "AWS::AccountId"
                    },
                    ":domain/",
                    {
                      "Ref": "ElasticsearchDomainName"
                    },
                    "/*"
                  ]
                ]
              }
            }
          ]
        },
        "AdvancedOptions": {
          "rest.action.multi.allow_explicit_index": "true"
        },
        "ElasticsearchClusterConfig": {
          "ZoneAwarenessEnabled": true,
          "DedicatedMasterCount": 3,
          "DedicatedMasterEnabled": true,
          "DedicatedMasterType": {
            "Fn::FindInMap": [
              "esmasterSizing",
              {
                "Fn::FindInMap": [
                  "esRegion2Type",
                  {
                    "Ref": "AWS::Region"
                  },
                  "type"
                ]
              },
              {
                "Ref": "ClusterSize"
              }
            ]
          },
          "InstanceCount": {
            "Fn::FindInMap": [
              "instanceCount",
              "elasticsearch",
              {
                "Ref": "ClusterSize"
              }
            ]
          },
          "InstanceType": {
            "Fn::FindInMap": [
              "esinstanceSizing",
              {
                "Fn::FindInMap": [
                  "esRegion2Type",
                  {
                    "Ref": "AWS::Region"
                  },
                  "type"
                ]
              },
              {
                "Ref": "ClusterSize"
              }
            ]
          }
        }
      }
    },
    "SolutionHelperRole": {
      "Type": "AWS::IAM::Role",
      "Properties": {
        "AssumeRolePolicyDocument": {
          "Version": "2012-10-17",
          "Statement": [{
            "Effect": "Allow",
            "Principal": {
              "Service": "lambda.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }]
        },
        "Path": "/",
        "Policies": [{
          "PolicyName": "Solution_Helper_Permissions",
          "PolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [{
              "Effect": "Allow",
              "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
              ],
              "Resource": {
                "Fn::Sub": "arn:aws:logs:${AWS::Region}:${AWS::AccountId}:log-group:/aws/lambda/*"
              }
            }]
          }
        }]
      }
    },
    "SolutionHelper": {
      "Type": "AWS::Lambda::Function",
      "Properties": {
        "Handler": "helper.lambda_handler",
        "Role": {
          "Fn::GetAtt": ["SolutionHelperRole", "Arn"]
        },
        "Description": "This function creates a CloudFormation custom lambda resource that creates custom lambda functions by finding and replacing specific values from existing lambda function code.",
        "Code": {
          "S3Bucket": {
            "Fn::Join": [
              "",
              [
                "%%DIST_BUCKET_NAME%%-",
                {
                  "Ref": "AWS::Region"
                }
              ]
            ]
          },
          "S3Key": "cost-optimization-monitor/%%VERSION%%/helper.zip"
        },
        "Environment": {
          "Variables": {
            "LOG_LEVEL": "INFO"
          }
        },
        "Runtime": "python3.7",
        "Timeout": 300
      }
    },
    "CreateUniqueID": {
      "Type": "Custom::CreateUUID",
      "Properties": {
        "ServiceToken": {
          "Fn::GetAtt": ["SolutionHelper", "Arn"]
        }
      }
    },
    "SendingAnonymousData": {
      "Type": "Custom::LoadLambda",
      "Condition": "SendData",
      "Properties": {
        "ServiceToken": {
          "Fn::GetAtt": ["SolutionHelper", "Arn"]
        },
        "SendAnonymousData": {
          "Fn::Join": [
            "",
            [
              "{ 'Solution' : '",
              "SO00010",
              "', ",
              "'UUID' : '",
              {
                "Fn::GetAtt": [
                  "CreateUniqueID",
                  "UUID"
                ]
              },
              "', ",
              "'Data': {",
              "'ClusterSize': '",
              {
                "Ref": "ClusterSize"
              },
              "'",
              "}",
              "}"
            ]
          ]
        }
      }
    }
  },
  "Outputs": {
    "SingleDashboardURL": {
      "Value": {
        "Fn::Join": [
          "",
          [
            "http://", {
              "Fn::GetAtt": ["ELB", "DNSName"]
            },
            "/_plugin/kibana/#/dashboard/Single-Account-Dashboard?_g=%28refreshInterval:%28display:Off,pause:!f,section:0,value:0%29,time:%28from:now%2FM,mode:quick,to:now%2FM%29%29"
          ]
        ]
      },
      "Description": "Single Account Dashboard"
    },
    "ConsolidatedDashboardURL": {
      "Value": {
        "Fn::Join": [
          "",
          [
            "http://", {
              "Fn::GetAtt": ["ELB", "DNSName"]
            },
            "/_plugin/kibana/#/dashboard/Consolidated-Account-Dashboard?_g=%28refreshInterval:%28display:Off,pause:!f,section:0,value:0%29,time:%28from:now%2FM,mode:quick,to:now%2FM%29%29"
          ]
        ]
      },
      "Description": "Consolidated Account Dashboard"
    },
    "BucketName": {
      "Description": "Bucket for storing Detailed Billing Records",
      "Value": {
        "Fn::If": [
          "UseExistingBucket",
          {
            "Ref": "DBRBucketName"
          },
          {
            "Ref": "S3DBRBucket"
          }
        ]
      }

    },
    "UUID": {
      "Description": "Newly created random anonymous UUID.",
      "Value": {
        "Fn::GetAtt": [
          "CreateUniqueID",
          "UUID"
        ]
      }
    }
  }
}
