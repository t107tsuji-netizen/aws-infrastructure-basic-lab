# AWS Security Basic Lab Architecture

```mermaid
flowchart TB
    admin[Admin User / 自分のPC]
    internet[Internet]

    subgraph aws[AWS Account]
        subgraph vpc[VPC]
            igw[Internet Gateway]

            subgraph public[Public Subnet]
                bastion[Bastion EC2]
            end

            subgraph private[Private Subnet]
                privateEc2[Private EC2]
            end

            bastionSg[Bastion Security Group<br/>SSH 22 from My IP]
            privateSg[Private EC2 Security Group<br/>SSH 22 from Bastion SG]
        end

        subgraph iam[IAM]
            ec2Role[EC2 IAM Role<br/>CloudWatchAgentServerPolicy<br/>AmazonSSMManagedInstanceCore]
        end

        subgraph logging[Logging / Audit]
            cloudtrail[CloudTrail<br/>Management Events]
            trailBucket[(S3 Bucket<br/>CloudTrail Logs<br/>Block Public Access)]
        end

        subgraph detection[Threat Detection]
            guardduty[GuardDuty<br/>Threat Detection]
            gdFinding[GuardDuty Findings<br/>Sample Findings]
        end

        subgraph config[AWS Config]
            configRecorder[AWS Config Recorder]
            configRules[Config Rules<br/>restricted-ssh<br/>s3-bucket-public-read-prohibited<br/>s3-bucket-public-write-prohibited<br/>cloudtrail-enabled]
            configBucket[(S3 Bucket<br/>AWS Config Logs)]
        end

        subgraph securityhub[Security Hub]
            secHub[Security Hub]
            standards[AWS Foundational<br/>Security Best Practices]
        end

        subgraph monitoring[Monitoring]
            cloudwatch[CloudWatch Alarm]
            cpuAlarm[CPUUtilization Alarm]
            statusAlarm[StatusCheckFailed Alarm]
        end
    end

    admin -->|SSH 22 / My IP| bastion
    internet --> igw
    igw --> bastion

    bastion -->|SSH 22| privateEc2

    bastionSg -. attached .-> bastion
    privateSg -. attached .-> privateEc2

    ec2Role -. attached .-> bastion
    ec2Role -. attached .-> privateEc2

    cloudtrail -->|stores logs| trailBucket
    cloudtrail -->|records API activities| aws

    configRecorder -->|records resource configurations| vpc
    configRecorder -->|delivers snapshots/history| configBucket
    configRecorder --> configRules

    guardduty --> gdFinding
    cloudtrail -. log source .-> guardduty

    gdFinding --> secHub
    configRules --> secHub
    secHub --> standards

    cloudwatch --> cpuAlarm
    cloudwatch --> statusAlarm
    cpuAlarm -. monitors .-> bastion
    cpuAlarm -. monitors .-> privateEc2
    statusAlarm -. monitors .-> bastion
    statusAlarm -. monitors .-> privateEc2
```
