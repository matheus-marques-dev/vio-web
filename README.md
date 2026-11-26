# VIO Web — Venda de Ingressos Online

Aplicação de venda de ingressos com front-end em **React**, API em **Node.js/Express** e deploy automatizado em uma máquina virtual na **Azure** usando **Docker Compose**, **Nginx** e **GitHub Actions**.

Projeto desenvolvido no 4º semestre de Desenvolvimento de Software Multiplataforma (Fatec Franca).

## Funcionalidades

- Exibe o status da API em tempo real na página inicial
- Lista os eventos disponíveis (nome, descrição, data, local e preço)
- Consulta os detalhes de um evento pelo ID

## Tecnologias

| Camada | Tecnologias |
|---|---|
| Front-end | React 19, Vite |
| Back-end | Node.js, Express 5 |
| Infraestrutura | Docker, Docker Compose, Nginx |
| CI/CD | GitHub Actions, deploy por tag com versionamento semântico |
| Nuvem | Máquina virtual Linux na Azure |

## Arquitetura

```
Navegador ──► Nginx (container frontend, porta 80)
                 ├── /       → arquivos do React gerados pelo Vite
                 └── /api/*  → proxy para o container backend (Express, porta 3000)
```

- O **frontend** usa um Dockerfile em duas etapas: o Node gera o build do Vite e o Nginx serve apenas os arquivos finais.
- O **Nginx** também funciona como proxy reverso, encaminhando as chamadas `/api` para o backend dentro da rede do Compose.
- O **backend** fica exposto apenas em `127.0.0.1`, sem acesso direto pela internet.

## API

| Método | Rota | Descrição |
|---|---|---|
| GET | `/api/status` | Status da API e versão publicada |
| GET | `/api/eventos` | Lista todos os eventos |
| GET | `/api/eventos/:id` | Retorna um evento pelo ID (404 se não existir) |

## Como rodar localmente

Pré-requisito: [Docker](https://www.docker.com/) com Docker Compose.

```bash
git clone https://github.com/matheus-marques-dev/vio-web.git
cd vio-web
docker compose up --build
```

Depois acesse `http://localhost`.

## Deploy contínuo

O workflow [`.github/workflows/release.yml`](.github/workflows/release.yml) publica uma nova versão sempre que uma tag no formato `vX.Y.Z` é enviada:

1. Valida os dados de acesso e a tag recebida.
2. Conecta na VM da Azure por SSH, com verificação da chave do servidor.
3. Busca a tag e confirma que ela corresponde ao commit do evento.
4. Constrói as imagens e sobe o backend antes do Nginx, aguardando cada serviço ficar saudável.
5. Testa `GET /api/status` pela porta do Nginx e só conclui se a API responder `online`.

```bash
git tag v1.0.2
git push origin v1.0.2
```

## Autor

**Matheus Junior Marques** · [Portfólio](https://matheus-marques.vercel.app) · [LinkedIn](https://www.linkedin.com/in/matheus-marques-34227532a/)
