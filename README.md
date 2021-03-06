# Gitlab Runner Auto Register

Created according to [official doc](https://docs.gitlab.com/runner/install/linux-repository.html) with additional flags to enable auto registry with given url and token.

The default image needs additional step of executing script from outside of container which makes it hard to automate deployment.

# Gitlab runner version

| tag               | version       |
| ---------------   | --------------|
| `0.0.3`           | 10.2.0        |
| `0.0.4`-`0.0.6`   | 10.4.0        |


# Running instructions

To run simply execute 

```bash
REGISTRATION_TOKEN=""
docker run -d -e CI_SERVER_URL="https://gitlab.com/" \
   -e REGISTRATION_TOKEN="$REGISTRATION_TOKEN" \
   --name gitlab-runner \
   -v /var/run/docker.sock:/var/run/docker.sock \
   flakm/gitlab-runner-auto-register:latest 
```

To create runner that registres itself in more then one project set `LOCKED_MODE` to false and provide both `ADMIN_TOKEN` and `PROJECTS_TO_REGISTER` variables.

```bash
REGISTRATION_TOKEN=""
ADMIN_TOKEN=""
PROJECTS_TO_REGISTER="5309683;4904307" 

docker run -d -e "CI_SERVER_URL=https://gitlab.com/" \
   -e REGISTRATION_TOKEN="$REGISTRATION_TOKEN" \
   -e ADMIN_TOKEN="$ADMIN_TOKEN" \
   -e PROJECTS_TO_REGISTER="$PROJECTS_TO_REGISTER" \
   -e LOCKED_MODE="false" \
   --name gitlab-runner-testing \
   -v /var/run/docker.sock:/var/run/docker.sock \
   flakm/gitlab-runner-auto-register:latest
```


To check logs and registration status use

```bash
docker logs gitlab-runner
```

To check registration details

```bash
docker exec -i gitlab-runner cat /etc/gitlab-runner/config.toml
```

Or register new runner

```bash
docker exec -i gitlab-runner gitlab-runner register
```

# Environment Variables

| Property              |  Description                                                                                                           | Default value                   | Required      |
| --------------------  | -----------------------------------------------------------------------------------------------------------------------| ------------------------------- | ------------- |
|`CI_SERVER_URL`        |  url of gitlab instance                                                                                                |                                 | true          |
|`REGISTRATION_TOKEN`   |  /settings/ci_cd tab of your project                                                                                   |                                 | true          |
|`PRIVILIGED_MODE`      |  should you need to run in priviliged mode set to true (otherwise defaults to false)                                   | false                           | false         |           
|`LOCKED_MODE`          |  should this runner be locked to this project or not                                                                   | true                            | false         |
|`ADMIN_TOKEN`          |  access token with admin privilages (if specified runner will be registered for projects`PROJECTS_TO_REGISTER`)        |                                 | false         |
|`PROJECTS_TO_REGISTER` |  project ids seperated by `;`                                                                                          |                                 | false         |
|`CUSTOM_RUNNER_NAME`   |  runner's description                                                                                                  | `$HOSTNAME`                     | false         |

For more information about the the environment variables execute `gitlab-runner register --help` inside docker


# Inspiration

Forked form https://github.com/pcodk/gitlab-runner-auto-register
https://github.com/ayufan/gitlab-ci-multi-runner
