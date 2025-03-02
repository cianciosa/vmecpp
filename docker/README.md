An Ubuntu 22.04 image with VMEC++ installed as a global Python package.

## Get the image

While the repo is still private, you need a GitHub token to pull this image:

1. go to https://github.com/settings/tokens
2. click generate new token -> generate new token (classic)
3. tick read:packages and generate token
4. copy the token ("ghp_...") -- this is your one chance
5. tell docker to log into the GitHub container registry using that token:
   `echo YOUR_GITHUB_TOKEN | docker login ghcr.io -u YOUR_GITHUB_USER --password-stdin`
7. you can now pull the image: `docker pull ghcr.io/jons-pf/vmecpp:latest`

## Use the image

Simple test: run SIMSOPT's ["QH fixed resolution" example](/examples/simsopt_qh_fixed_resolution.py) with VMEC++:

```shell
docker run -it --rm ghcr.io/jons-pf/vmecpp:latest
# now inside the docker container (by default we'll be inside the vmecpp repo sources):
python examples/simsopt_qh_fixed_resolution.py
```

To run VMEC++ on configurations you have on your host system, e.g. in a directory `data_dir`,
you could mount that directory onto the docker container and use VMEC++'s CLI API:

```shell
docker run -it --rm -v/absolute/path/to/data_dir:/data_dir ghcr.io/jons-pf/vmecpp:latest
# now inside the docker container we can run the VMEC++ CLI:
python -m vmecpp /data_dir/input.xyz
```


## For developers: manually pushing a new image

1. create a GitHub token like in [Get the image](#get-the-image), but check the `write:packages` and `repo` permissions
2. log into the GitHub container registry like in [Get the image](#get-the-image)
3. build the docker image
  - `env GITHUB_TOKEN=ghp_xyz docker build --tag=ghcr.io/jons-pf/vmecpp:latest --secret id=GITHUB_TOKEN .`
  - on systems with newer docker, you might need `docker buildx build` instead of just `docker build`
4. push the docker image
  - `docker push ghcr.io/jons-pf/vmecpp:latest`
