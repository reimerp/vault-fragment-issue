In vault you can use fragments to a secret path to group logically related secrets, eg a user "admin" has a "token" and a  "password", or
    a tls cert has a key, a root pem and a pem file which can only exist together.

In this example an existing
`ref+vault://some/path/prom#imagepull`
secret is used

# ok
`helmfile -l name=values-ok template | grep -A 1 imagePullSecrets`
# error "no secret found for path" as expected
`helmfile -l name=values-invalid-path template | grep -A 1 imagePullSecrets`

# error, templates to ""
`helmfile -l name=values-invalid-fragment template | grep -A 1 imagePullSecrets`

## but errorlevel is correct:
```
$ helm secrets decrypt values_invalid_fragment.yaml ; echo $?
[helm-secrets] vals error: expand vault://.../prom#image_pull: no value found for key image_pull
1
```

or
```
$ helm template --values secrets://values_invalid_fragment.yaml prometheus-community/prometheus ; echo $?
[helm-secrets] vals error: expand vault://.../prom#image_pull: no value found for key image_pull
Error: plugin "scripts/run.sh downloader" exited with error
1
```