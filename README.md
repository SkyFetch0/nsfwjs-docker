# nsfwjs-docker

Self-Hosted NSFW Detection API powered by Docker ([NSFWJS](https://github.com/infinitered/nsfwjs) under the hood).

## Features ✨

- ℹ️ Returns predictions for `Neutral`, `Drawing`, `Sexy`, `Hentai` and `Porn`
- 🎯 Pretty accurate (~93%)
- 🖼️ Supports different image formats
- ⚡ 250ms to make predictions for a single image
- 🔐 Bearer token authentication

## Installation ⚙️

### 1. Clone the repository

```shell
git clone https://github.com/skyfetch0/nsfwjs-docker.git
cd nsfwjs-docker
```

### 2. Build the Docker image

```shell
docker build -t skyfetch0/nsfwjs .
```

### 3. Run the container

With a custom API key:

```shell
docker run -p 3333:3333 -e API_KEY=your_secret_key -d --name nsfwjs skyfetch0/nsfwjs
```

Without an API key (auto-generated at startup):

```shell
docker run -p 3333:3333 -d --name nsfwjs skyfetch0/nsfwjs
```

> If you are deploying in production, pass the `--restart always` flag to automatically restart the container when the server reboots.

```shell
docker run -p 3333:3333 -e API_KEY=your_secret_key --restart always -d --name nsfwjs skyfetch0/nsfwjs
```

## Authentication 🔐

All endpoints require a Bearer token. Set your API key via the `API_KEY` environment variable when starting the container.

If `API_KEY` is not provided, one is automatically generated at startup and printed to the container logs:

```shell
docker logs nsfwjs
```

```
╔══════════════════════════════════════════════════════════╗
║  API_KEY ortam değişkeni bulunamadı, otomatik oluşturuldu ║
║  API_KEY: a3f9c2b1...                                     ║
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

`POST` request to `/single/multipart-form` with an image in the `content` field.

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

`POST` request to `/multiple/multipart-form` with images in the `contents` field.

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

## License

[MIT](LICENSE)