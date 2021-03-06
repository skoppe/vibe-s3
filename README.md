# AWS Client
========

Client for AWS services. Currently only supports S3.

## Usage

```dlang
import aws.s3;
import aws.aws;
import aws.credentials;
import std.algorithm : joiner;
import std.array : array;

void main() {
    import std.array : appender;
    import std.stdio;
    auto creds = new StaticAWSCredentials("test", "test");

    auto region = "us-east-1";
    auto endpoint = "http://localhost:4566";
    auto s3 = new S3(endpoint,region,creds);

    s3.createBucket("test-bucket");
    s3.upload("test-bucket", "myfile", cast(ubyte[])[48,49,50,51,52,53]);
    s3.download("test-bucket", "myfile").receiveAsRange.joiner().array().writeln();
    auto directories = appender!string;
    auto files = appender!string;

    string marker = null;
    while(true)
        {
            auto result = s3.list("test-bucket", "/", null,marker,100);
            foreach(directory; result.commonPrefixes)
                directories.put(directory~"\n");

            foreach(file; result.resources)
                files.put(file.key~"\n");

            if (result.isTruncated)
                marker = result.nextMarker;
            else
                break;
        }


    writeln(directories);
    writeln(files);
}
```
