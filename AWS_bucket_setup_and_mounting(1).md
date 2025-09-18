**AWS bucket setup and mounting in it ubuntu server , setting up
permission**

**Creating user in AWS and create Access key and Secret key**

-   Navigate to IAM service.

-   Click on Users in the left sidebar.

-   Create User and provide name with Set permission

-   Point to create access key at right top

-   Select the dropbox which is required (Other) and click next

-   You will get the Access and Secret key (Download .csv file)

**Creating AWS bucket**

-   Login to AWS account

-   Point to S3 and create bucket

-   Bucket type --- General purpose

-   Bucket name --- Provide name

-   Object Ownership --- ACLs disabled

-   Bucket Versioning --- Disable

-   Rest leave everything default and create bucket

**Create user in AWS**

-   Go to IAM user

-   Under Access management go to user

-   Click on create user and provide a username

-   Permissions can be set later, click create user

**Create Group in AWS**

-   Go to IAM user

-   Under Access management go to User groups

-   Create group and provide a name

**Create IAM user policy**

-   Go to IAM user

-   Under Access management go to Polices

-   Click on create policy and point to JSON

-   In Policy editor , specify the policy below mentioned (according to
    > your bucket)

> {
>
> \"Version\": \"2012-10-17\",
>
> \"Statement\": \[
>
> {
>
> \"Sid\": \"AllowS3Access\",
>
> \"Effect\": \"Allow\",
>
> \"Action\": \[
>
> \"s3:PutObject\",
>
> \"s3:GetObject\"
>
> \],
>
> \"Resource\": \"arn:aws:s3:::tnd-bucket-styra/\*\"
>
> },
>
> {
>
> \"Sid\": \"AllowListBucket\",
>
> \"Effect\": \"Allow\",
>
> \"Action\": \"s3:ListBucket\",
>
> \"Resource\": \"arn:aws:s3:::tnd-bucket-styra\"
>
> },
>
> {
>
> \"Sid\": \"AllowSTS\",
>
> \"Effect\": \"Allow\",
>
> \"Action\": \[
>
> \"sts:AssumeRole\",
>
> \"sts:GetCallerIdentity\"
>
> \],
>
> \"Resource\": \"\*\"
>
> }
>
> \]
>
> }

-   **Provide the name for your policy and create policy**

**Create Permission policy for YOUR USER**

-   Go to Access management --- User --- Permissions ---Add permissions
    > --- Create inline policy --- JSON

-   In Policy editor, add the below specified policy (update accordingly
    > to your bucket)

> {
>
> \"Version\": \"2012-10-17\",
>
> \"Statement\": \[
>
> {
>
> \"Sid\": \"VisualEditor0\",
>
> \"Effect\": \"Allow\",
>
> \"Action\": \[
>
> \"s3:PutObject\",
>
> \"s3:GetObject\",
>
> \"s3:ListBucket\"
>
> \],
>
> \"Resource\": \"arn:aws:s3:::tnd-bucket-styra/\"
>
> },
>
> {
>
> \"Sid\": \"VisualEditor1\",
>
> \"Effect\": \"Allow\",
>
> \"Action\": \[
>
> \"sts:AssumeRole\",
>
> \"sts:GetCallerIdentity\"
>
> \],
>
> \"Resource\": \"\*\"
>
> }
>
> \]
>
> }

-   Click next and provide a name for your policy

-   Add another policy for S3 by clicking on Add permission option

-   Add another policy for Administrator Access by clicking on Add
    > permission option

> ![](media/image1.png){width="7.28125in" height="0.8302569991251093in"}

**Add USER to GROUP**

-   Go to user

-   Under user go to group

-   See for the option add user to group

-   Add the the created group to that user

**Create ROLE and attach permission to ROLES**

-   Go to IAM policy

-   Under Access management go to role and create role

-   Select the Trusted Entity Type as AWS service

-   Select the Use case as S3

-   Add permission for Administrator Access

-   Provide a role name

-   For trusted entity policy provide the below specified conditions
    > (AWS ARN replace)

> {
>
> \"Version\": \"2012-10-17\",
>
> \"Statement\": \[
>
> {
>
> \"Effect\": \"Allow\",
>
> \"Principal\": {
>
> \"Service\": \"s3.amazonaws.com\",
>
> \"AWS\": \"arn:aws:iam::247789200269:user/karthik\"
>
> },
>
> \"Action\": \"sts:AssumeRole\"
>
> }
>
> \]
>
> }

**Attach policy to ROLE**

-   Point to Role in Access managemnet

-   Go to Add permissions -- Add Policy

-   Select the policy which is created for earlier in POLICY

-   Attach the policy to ROLE

**Create a CORS Policy for bucket**

-   Go to Access management --- bucket --- Permissions --- Scroll down
    > to CORS

-   Add the below content to CORS policy to uploads the file

\[

{

\"AllowedHeaders\": \[

\"\*\"

\],

\"AllowedMethods\": \[

\"GET\",

\"PUT\",

\"HEAD\",

\"DELETE\",

\"HEAD\"

\],

\"AllowedOrigins\": \[

\"https://cmss.styra.in\"

\],

\"ExposeHeaders\": \[

\"Access-Control-Allow-Origin\",

\"ETag\"

\],

\"MaxAgeSeconds\": 3000

}

\]

**Please note to replace the domain URL under Allowed Origins**

**Instaling and configuring AWS CLI**

-   Run the below command in the ubuntu server where you want to install
    > AWS CLI

-   sudo apt install -y curl unzip

-   curl \"https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip\" -o
    > \"awscliv2.zip\"

-   unzip awscliv2.zip

-   sudo ./aws/install

-   aws \--version

-   aws configure

    -   Access key

    -   Secret Key

    -   Region

    -   json

![](media/image2.png){width="7.283464566929134in"
height="1.0694444444444444in"}
