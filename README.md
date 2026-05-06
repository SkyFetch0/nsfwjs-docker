# nsfwjs-docker [![Docker Pulls](https://img.shields.io/docker/pulls/andresribeiroo/nsfwjs.svg)](https://hub.docker.com/r/andresribeiroo/nsfwjs)

Docker-Powered Self-Hosted NSFW Detection API ([NSFWJS](https://github.com/infinitered/nsfwjs) under the hood). You can find it on the Docker Hub [here](https://hub.docker.com/r/andresribeiroo/nsfwjs).

## Features ✨

- ℹ️ Return predictions for `Neutral`, `Drawing`, `Sexy`, `Hentai` and `Porn`
- 🎯 Pretty accurate (~93%)
- 🖼️ Supports different image formats
- ⚡ 250ms to make predictions to a single image
- 🔐 Bearer token authentication

## Installation ⚙️

```shell
docker run -p 3333:3333 -d --name nsfwjs andresribeiroo/nsfwjs:2.0
```

If you are deploying in production, you will probably want to pass the `--restart always` flag to start the container whenever the server restarts.

## Authentication 🔐

All endpoints require a Bearer token. Set your API key via the `API_KEY` environment variable:

```shell
docker run -p 3333:3333 -e API_KEY=your_secret_key -d --name nsfwjs andresribeiroo/nsfwjs:2.0
```

If `API_KEY` is not provided, one is automatically generated at startup and printed to the container logs:

```shell
docker logs nsfwjs
```

```
╔══════════════════════════════════════════════════════════╗
║  API_KEY ortam değişkeni bulunamadı, otomatik oluşturuldu ║
║  API_KEY: a3f9c2...                                       ║
║  Bu anahtarı güvenli bir yerde saklayın!                  ║
╚══════════════════════════════════════════════════════════╝
```

Include the key in every request using the `Authorization` header:

```
Authorization: Bearer your_secret_key
```

Requests without a valid token will receive a `401 Unauthorized` response.

## Usage 🔨

### One image

`POST` request to `/single/multipart-form` sending an image in the `content` field.

```shell
curl -X POST http://localhost:3333/single/multipart-form \
  -H "Authorization: Bearer your_secret_key" \
  -F "content=@image.jpg"
```

```json
{
  "prediction": [
    {
      "className": "Neutral",
      "probability": 0.6371303796768188
    },
    {
      "className": "Drawing",
      "probability": 0.3603636920452118
    },
    {
      "className": "Hentai",
      "probability": 0.0024505197070538998
    },
    {
      "className": "Sexy",
      "probability": 0.00003775714503717609
    },
    {
      "className": "Porn",
      "probability": 0.000017730137187754735
    }
  ]
}
```

### Multiple images

`POST` request to `/multiple/multipart-form` sending images in the `contents` field.

```shell
curl -X POST http://localhost:3333/multiple/multipart-form \
  -H "Authorization: Bearer your_secret_key" \
  -F "contents=@image1.jpg" \
  -F "contents=@image2.jpg"
```

```json
{
  "predictions": [
    [
      {
        "className": "Neutral",
        "probability": 0.6371303796768188
      },
      {
        "className": "Drawing",
        "probability": 0.3603636920452118
      },
      {
        "className": "Hentai",
        "probability": 0.0024505197070538998
      },
      {
        "className": "Sexy",
        "probability": 0.00003775714503717609
      },
      {
        "className": "Porn",
        "probability": 0.000017730137187754735
      }
    ],
    [
      {
        "className": "Neutral",
        "probability": 0.9498893618583679
      },
      {
        "className": "Drawing",
        "probability": 0.04626458138227463
      },
      {
        "className": "Hentai",
        "probability": 0.00267870188690722
      },
      {
        "className": "Sexy",
        "probability": 0.0008198379655368626
      },
      {
        "className": "Porn",
        "probability": 0.0003475486591923982
      }
    ]
  ]
}
```