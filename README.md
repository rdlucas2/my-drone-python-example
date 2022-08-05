# my-drone-python-example

## requirements:
- download and setup ngrok
- setup github oauth app and get client id and secret, homepage url and authorization callback url must match ngrok generated url, auth url is host with /login
- openssl
- docker

start ngrok:
```
ngrok http 80
```

generate shared secret with openssl:
```
openssl rand -hex 16
```

server - docker run command (backticks for powershell, switch to \ if on linux):
```
docker run `
  --volume=/var/lib/drone:/data `
  --env=DRONE_GITHUB_CLIENT_ID=CLIENT_ID_HERE `
  --env=DRONE_GITHUB_CLIENT_SECRET=CLIENT_SECRET_HERE `
  --env=DRONE_RPC_SECRET=OPENSSL_SHARED_SECRET_HERE `
  --env=DRONE_SERVER_HOST=NGROK_URL_HERE `
  --env=DRONE_SERVER_PROTO=https `
  --publish=80:80 `
  --publish=443:443 `
  --restart=always `
  --detach=true `
  --name=drone `
  drone/drone:2
```

runner - docker run command:
```
docker run --detach `
  --volume=/var/run/docker.sock:/var/run/docker.sock `
  --env=DRONE_RPC_PROTO=https `
  --env=DRONE_RPC_HOST=NGROK_URL_HERE `
  --env=DRONE_RPC_SECRET=OPENSSL_SHARED_SECRET_HERE `
  --env=DRONE_RUNNER_CAPACITY=2 `
  --env=DRONE_RUNNER_NAME=my-first-runner `
  --publish=3000:3000 `
  --restart=always `
  --name=runner `
  drone/drone-runner-docker:1
```
