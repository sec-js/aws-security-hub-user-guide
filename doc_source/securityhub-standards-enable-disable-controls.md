# Enabling and disabling controls in all standards<a name="securityhub-standards-enable-disable-controls"></a>

When you enable a standard in AWS Security Hub, all of the controls that apply to it are automatically enabled for that standard \(the exception to this is service\-managed standards\)\. You can then disable and re\-enable specific controls\.

You must enable and disable controls separately in each AWS account and AWS Region\. When you enable or disable a control, it only impacts the current account and Region\.

**Note**  
You can enable and disable controls in each Region by using the Security Hub console, Security Hub API, or AWS CLI\. If you have set an aggregation Region, you see controls from all linked Regions\. If a control is available in a linked Region but not in the aggregation Region, you cannot enable or disable that control from the aggregation Region\. For multi\-account and multi\-Region control disablement scripts, refer to [ Disabling Security Hub controls in a multi\-account environment](http://aws.amazon.com/blogs/security/disabling-security-hub-controls-in-a-multi-account-environment/)\.

## Enabling a control in all standards<a name="enable-controls-all-standards"></a>

When you enable a control in a standard, Security Hub starts to run security checks for the control and generate findings for the control\. Security Hub also includes the [control status](controls-overall-status.md#controls-overall-status-values) in the calculation of the overall security score and standard security scores\. If you turn on consolidated control findings, you'll receive a single finding for a security check even if you've enabled the related control in multiple standards\. For more information, see see [Consolidated control findings](controls-findings-create-update.md#consolidated-control-findings)\.

Follow these steps to enable a Security Hub control in *all* of your enabled standards\. For instructions on enabling a control in a *specific* standard, see [Enabling a control in a specific standard](controls-configure.md#enable-controls-standard)\.

------
#### [ Security Hub console ]

1. Open the AWS Security Hub console at [https://console\.aws\.amazon\.com/securityhub/](https://console.aws.amazon.com/securityhub/)\.

1. Choose **Controls** from the navigation pane\.

1. Choose the **Disabled** tab\.

1. Choose the option next to a control\.

1. Choose **Enable Control** \(this option doesn't appear for a control that's already enabled\)\.

------
#### [ Security Hub API ]

1. Run `[https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_ListStandardsControlAssociations.html](https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_ListStandardsControlAssociations.html)`, providing a specific control ID to return the current enablement status of the control in each standard\. Provide standard\-agnostic security control IDs, not standard\-specific control IDs\.

   **Example request:**

   ```
   {
       "SecurityControlId": "IAM.1"
   }
   ```

1. Run `[https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_BatchUpdateStandardsControlAssociations.html](https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_BatchUpdateStandardsControlAssociations.html)`\. Provide the Amazon Resource Name \(ARN\) of any standards that the control isn't enabled in\. To obtain standard ARNs, run [https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_DescribeStandards.html](https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_DescribeStandards.html)\.

1. Set the `AssociationStatus` parameter equal to `ENABLED`\. If you follow these steps for a control that's already enabled, the API returns an HTTP status code 200 response\.

   **Example request:**

   ```
   {
       "StandardsControlAssociationUpdates": [{"SecurityControlId": "IAM.1", "StandardsArn": "arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0", "AssociationStatus": "ENABLED"}, {"SecurityControlId": "IAM.1", "StandardsArn": "arn:aws:securityhub:::standards/aws-foundational-security-best-practices/v/1.0.0", "AssociationStatus": "ENABLED"}]
   }
   ```

------
#### [ AWS CLI ]

1. Run the `[https://docs.aws.amazon.com/cli/latest/reference/securityhub/list-standards-control-associations.html](https://docs.aws.amazon.com/cli/latest/reference/securityhub/list-standards-control-associations.html)` command, providing a specific control ID to return the current enablement status of the control in each standard\. Provide standard\-agnostic security control IDs, not standard\-specific control IDs\.

   ```
   aws securityhub  --region us-east-1 [https://docs.aws.amazon.com/cli/latest/reference/securityhub/list-standards-control-associations.html](https://docs.aws.amazon.com/cli/latest/reference/securityhub/list-standards-control-associations.html) --security-control-id CloudTrail.1
   ```

1. Run the `[https://docs.aws.amazon.com/cli/latest/reference/securityhub/batch-update-standards-control-associations.html](https://docs.aws.amazon.com/cli/latest/reference/securityhub/batch-update-standards-control-associations.html)` command\. Provide the Amazon Resource Name \(ARN\) of any standards that the control isn't enabled in\. To obtain standard ARNs, run the `describe-standards` command\.

1. Set the `AssociationStatus` parameter equal to `ENABLED`\. If you follow these steps for a control that's already enabled, the command returns an HTTP status code 200 response\.

   ```
   aws securityhub  --region us-east-1 [https://docs.aws.amazon.com/cli/latest/reference/securityhub/batch-update-standards-control-associations.html](https://docs.aws.amazon.com/cli/latest/reference/securityhub/batch-update-standards-control-associations.html) --standards-control-association-updates '[{"SecurityControlId": "CloudTrail.1", "StandardsArn": "arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0", "AssociationStatus": "ENABLED"}, {"SecurityControlId": "CloudTrail.1", "StandardsArn": "arn:aws:securityhub:::standards/cis-aws-foundations-benchmark/v/1.4.0", "AssociationStatus": "ENABLED"}]'
   ```

------

## Disabling a control in all standards<a name="disable-controls-all-standards"></a>

One way to disable a control is by disabling all standards that the control applies to\. When you disable a standard, all of the controls that apply to the standard are disabled \(however, those controls may still remain enabled in other standards\)\. For information about disabling a standard, see [Enabling and disabling security standards](securityhub-standards-enable-disable.md)\.

When you disable a control by disabling all standards it applies to, the following occurs:
+ Security checks for the control are no longer performed\.
+ No additional findings are generated for that control\.
+ Existing findings are archived automatically after 3\-5 days \(note that this is best effort and not guaranteed\)\.
+ The related AWS Config rules that Security Hub created are removed\.

When you disable a standard, Security Hub does not track which controls were disabled\. If you subsequently enable the standard again, all of the controls that apply to it are automatically enabled\. In addition, disabling a control is a one\-time action\. Suppose you disable a control, and then you enable a standard which was previously disabled\. If the standard includes that control, it will be enabled in that standard\. When you enable a standard in Security Hub, all of the controls that apply to that standard are automatically enabled\.

Instead of disabling a control by disabling all standards it applies to, you can just disable the control in one or more specific standards\. If you do this, Security Hub won't run security checks for the control for the standards you disabled it in, so it won't affect the security score for those standards\. However, Security Hub will continue running security checks for the control if it is enabled in other standards\.

To reduce finding noise, it can be useful to disable controls that aren't relevant to your environment\. For recommendations of which controls to disable, see [Security Hub controls that you might want to disable](controls-to-disable.md)\.

Follow these steps to disable a Security Hub control in *all* standards\. To disable a control in a *specific* standard, see [Disabling a control in a specific standard](controls-configure.md#disable-controls-standard)\.

------
#### [ Security Hub console ]

1. Open the AWS Security Hub console at [https://console\.aws\.amazon\.com/securityhub/](https://console.aws.amazon.com/securityhub/)\.

1. Choose **Controls** from the navigation pane\.

1. Choose the option next to a control\.

1. Choose **Disable Control** \(this option doesn't appear for a control that's already disabled\)\.

1. Select a reason for disabling the control, and confirm by choosing **Disable**\.

------
#### [ Security Hub API ]

1. Run `[https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_ListStandardsControlAssociations.html](https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_ListStandardsControlAssociations.html)`, providing a specific control ID to return the current enablement status of the control in each standard\. Provide standard\-agnostic security control IDs, not standard\-specific control IDs\.

   **Example request:**

   ```
   {
       "SecurityControlId": "IAM.1"
   }
   ```

1. Run `[https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_BatchUpdateStandardsControlAssociations.html](https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_BatchUpdateStandardsControlAssociations.html)`\. Provide the ARN of any standards that the control is enabled in\. To obtain standard ARNs, run [https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_DescribeStandards.html](https://docs.aws.amazon.com/securityhub/1.0/APIReference/API_DescribeStandards.html)\.

1. Set the `AssociationStatus` parameter equal to `DISABLED`\. If you follow these steps for a control that's already disabled, the API returns an HTTP status code 200 response\.

   **Example request:**

   ```
   {
       "StandardsControlAssociationUpdates": [{"SecurityControlId": "IAM.1", "StandardsArn": "arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0", "AssociationStatus": "DISABLED", "UpdatedReason": "Not applicable to environment"}, {"SecurityControlId": "IAM.1", "StandardsArn": "arn:aws:securityhub:::standards/aws-foundational-security-best-practices/v/1.0.0", "AssociationStatus": "DISABLED", "UpdatedReason": "Not applicable to environment"}}]
   }
   ```

------
#### [ AWS CLI ]

1. Run the `[https://docs.aws.amazon.com/cli/latest/reference/securityhub/list-standards-control-associations.html](https://docs.aws.amazon.com/cli/latest/reference/securityhub/list-standards-control-associations.html)` command , providing a specific control ID to return the current enablement status of the control in each standard\. Provide standard\-agnostic security control IDs, not standard\-specific control IDs\.

   ```
   aws securityhub  --region us-east-1 list-standards-control-associations --security-control-id CloudTrail.1
   ```

1. Run `[https://docs.aws.amazon.com/cli/latest/reference/securityhub/batch-update-standards-control-associations.html](https://docs.aws.amazon.com/cli/latest/reference/securityhub/batch-update-standards-control-associations.html)`\. Provide the ARN of any standards that the control is enabled in\. To obtain standard ARNs, run the `describe-standards` command\.

1. Set the `AssociationStatus` parameter equal to `DISABLED`\. If you follow these steps for a control that's already disabled, the command returns an HTTP status code 200 response\.

   ```
   aws securityhub  --region us-east-1 batch-update-standards-control-associations --standards-control-association-updates '[{"SecurityControlId": "CloudTrail.1", "StandardsArn": "arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0", "AssociationStatus": "DISABLED", "UpdatedReason": "Not applicable to environment"}, {"SecurityControlId": "CloudTrail.1", "StandardsArn": "arn:aws:securityhub:::standards/cis-aws-foundations-benchmark/v/1.4.0", "AssociationStatus": "DISABLED", "UpdatedReason": "Not applicable to environment"}]'
   ```

------