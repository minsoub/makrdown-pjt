Title: How to use the Cloudcraft.io
Date: 2021-01-04 08:53
Category: AWS Architecure

# cloudcraft.io에서의 AWS 연결 #

## 1. LIVE 탭에서 AWS 계정 연결 ##
- ADD AWS ACCOUNT 버튼 클릭해서 AWS 계정을 연결
- AWS IME Role 설정 
  Other AWS ID 연결 및 권한 설정
  ![system schema](images/add_ime_role.png)
  ![system schema](images/readonly_access.png)
  ![system schema](images/create_role.png)
## 2. Blueprint create ##
- AWS 연결 후 SCAN NOW 버튼을 클릭해서 기존 AWS 서비스를 조회한다. 
- AUTO LAYOUT 버튼을 클릭해서 기존에 등록된 AWS 서비스로 레이아웃을 그린다
  ![system schema](images/layout-001.png)
## 3. BUDGET 확인 ##
- BUDGET 탭을 클릭해서 현재 HIST에서 사용하는 BUDGET 확인
  ![system schema](images/layout-002.png)
## 4. EXPORT ##
- 생성된 Layout을 EXPORT 기능을 사용해서 Terraform code로 export 한다.
- 상단 오른쪽 메뉴에서 EXPORT 버튼을 클릭한 후 Terraform code export 메뉴 클릭
  ![system schema](images/layout-003.png)
- 생성된 Terraform code는 압축파일로 생성되어 다운로드된다. 
  각 생성은 서비스별로 Terraform code가 생성되고 해당 폴더에 terragrunt.hcl 파일을 확인할 수 있다.  
## 5. 생성된 Terraform code 확인을 위해서 
#### Install Terragrunt 0.22 or newer 설치 
- https://terragrunt.gruntwork.io/docs/getting-started/install/ 참조
- Chocolatey 패키지 Manager를 사용해서 Install (관리자 권한으로 수행)  
  choco install terragrunt  
```
C:\Users\JMS>choco install terragrunt
Chocolatey v0.10.15
Installing the following packages:
terragrunt
By installing you accept licenses for the packages.

terraform v0.14.3 [Approved]
terraform package files install completed. Performing other installation steps.
The package terraform wants to run 'chocolateyInstall.ps1'.
Note: If you don't run this script, the installation will fail.
Note: To confirm automatically next time, use '-y' or consider:
choco feature enable -n allowGlobalConfirmation
Do you want to run the script?([Y]es/[A]ll - yes to all/[N]o/[P]rint): y

Removing old terraform plugins
Downloading terraform 64 bit
  from 'https://releases.hashicorp.com/terraform/0.14.3/terraform_0.14.3_windows_amd64.zip'
Progress: 100% - Completed download of C:\Users\JMS\AppData\Local\Temp\chocolatey\terraform\0.14.3\terraform_0.14.3_windows_amd64.zip (32.33 MB).
Download of terraform_0.14.3_windows_amd64.zip (32.33 MB) completed.
Hashes match.
Extracting C:\Users\JMS\AppData\Local\Temp\chocolatey\terraform\0.14.3\terraform_0.14.3_windows_amd64.zip to C:\ProgramData\chocolatey\lib\terraform\tools...
C:\ProgramData\chocolatey\lib\terraform\tools
 ShimGen has successfully created a shim for terraform.exe
 The install of terraform was successful.
```
- Terraform code에서의 AWS account configuration  
  terragrunt.hcl 편집.  
```python
provider "aws" {
  region     = "us-west-2"
  access_key = "my-access-key"
  secret_key = "my-secret-key"
}
```
- 생성된 Terraform code를 사용해서 AWS에 적용하기 위한 방법
```
$ cd ap-northeast-2
$ terragrunt apply-all
```
```
$ cd ap-northeast-2/icheck-api-container-new-nlb
$ terragrunt apply
```

## 6. Terragrunt 실행시 
- MFA 정책이 있으면 기본 AWS 권한으로 에러가 발생할 수 있음.
```
session=$(aws sts get-session-token --profile $AWS_PROFILE --serial-number $SECURITY_DEVICE_ARN --token-code $MFA_TOKEN)
export AWS_ACCESS_KEY_ID=$(echo $session | jq  -r .Credentials.AccessKeyId)
export AWS_SECRET_ACCESS_KEY=$(echo $session | jq  -r .Credentials.SecretAccessKey)
export AWS_SESSION_TOKEN=$(echo $session | jq -r .Credentials.SessionToken)
export AWS_MFA_SERIAL_NUMBER=${MFA_TOKEN}
```

![system schema](images/JMS.png)
