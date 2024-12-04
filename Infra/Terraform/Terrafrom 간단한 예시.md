
```tf
terraform {

required_providers {

	aws = {

			source = "hashicorp/aws"

			version = "~> 5.0"

		}

	}


	required_version = ">= 1.2.0"
}

provider "aws" {

	region = "ap-northeast-2"

}

  

resource "aws_instance" "app_server" {

	ami = "ami-@@@@@@@@@@@@@"
	
	instance_type = "t3.nano"
	
	subnet_id = "subnet-@@@@@@@@@@@@"

  

	tags = {

		Name = "Terraform Demo Server"

	}

}
```


**첫 tf 파일일 시 필요한듯**
```shell
terraform init
```

**어떤 변경사항이 있는지 확인**
```shell
terraform plan 
```

**변경사항 반영**
```shell
terrafrom apply 
```

**삭제**
```shell
terraform destroy
```

