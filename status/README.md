# Gogo Status

This is a convenience package for users of `gogo/protobuf` to be able to use
their `gogo/protobuf` generated message easily and transparently with the
gRPC status error structure.

## Use

Use as you would the normal `grpc/status` package:

```go
return status.Error(codes.NotFound, "no such user")
```

```go
st := status.New(codes.FailedPrecondition, "wrong user role")
detSt, err := st.WithDetails(&rpc.BadRequest{
    FieldViolations: []*rpc.BadRequest_FieldViolation{
        {
            Field:       "role",
            Description: "The first user created must have the role of an ADMIN",
        },
    },
})
if err == nil {
    return detSt.Err()
}
return st.Err()
```

## License

The code is 95% copied from the official gRPC status package, so the gRPC
License applies.

### Changes

The changes applies include changing the use of the
`golang/protobuf` packages to `gogo/protobuf`, and changing the
generated files from `google.golang.org/genproto/googleapis` to
`github.com/goog/googleapis/`.

We've also created an implicit interface fulfilled by all `gogo/status`
errors, for use in gRPC;

```go
type StatusError interface {
    error
    Code() codes.Code
    Message() string
    Details() []interface{ TypeUrl() string, Value() []byte}
}
```