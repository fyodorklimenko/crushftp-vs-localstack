## Goal
Create VFS item with S3 type connected with S3 bucket in [LocalStack](https://localstack.cloud/).

## Used tools
- Docker;
- AWS CLI.

## Versions
```
$ docker --version
Docker version 19.03.12, build 48a66213fe

$ aws --version
aws-cli/1.16.190 Python/3.6.0 Windows/10 botocore/1.12.180
```

## Setup
1. Run compose command to create containers with CrushFTP and LocalStack:
```bash
docker-compose -f "docker-compose.yml" up -d --build
```

2. Check if `my-bucket` S3 bucket exists in running `localstack` container:
```bash
aws --endpoint-url=http://localhost:4566 s3api list-buckets --query "Buckets[].Name"
```

Expected result:
```bash
[
    "my-bucket"
]
```

If your result is an empty array then you may need to wait a few more seconds.  
LocalStack delays on init.

3. Put file into LocalStack S3:
```bash
aws --endpoint-url=http://localhost:4566 s3api put-object --bucket=my-bucket --key=my-file --body=file
```

4. Check if `crushftp` container has got access to `my-bucket` in `localstack` container:

```bash
docker exec -it crushftp bash # connect to the crushftp container.
curl -H "Authorization: AWS qwerty:qwerty" http://localstack:4566/my-bucket/my-file # get file from localstack's S3.
```

The expected result of the last command:
```
As Gregor Samsa awoke one morning from uneasy dreams he found himself transformed in his bed into a gigantic insect.bash-5.0
```

In `curl` command you must set `Authorization` header with random user/password values.  
That's how LocalStack works.

## Summary of the setup
- CrushFTP is running in `crushftp` container.
- LocalStack with S3 service is running in `localstack` container.
- `my-bucket` S3 bucket is created in LocalStack S3 service.
- `crushftp` container has got access to the `localstack`'s S3.

## Create VFS item
1. Go to http://localhost:8080/WebInterface/UserManager/index.html to manage user's stuff.
2. Enter `admin` as a user name and `admin` as a password.
3. Click `Create new Remote Item` button, fill inputs, and test it.

![test-vfs-item](screenshots/test-vfs-item.png?raw=true "Test VFS item")

Testing fails but you are still able to create the remote item.

4. Click `OK` at the modal window to create VFS item.
5. Update VFS item permissions.

![vfs-item-permissions](screenshots/vfs-item-permissions.png?raw=true "VFS item permissions")

6. Click `Save` button at the bottom.
7. Go to http://localhost:8080/ to upload some files into `directory`.
8. Click `Add files...` button.
9. Upload file.

![Upload file result](screenshots/upload-file-result.png?raw=true "Upload file result")

## Summary of the VFS item creation
It does not work.