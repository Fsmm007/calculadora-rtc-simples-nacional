# calculadora-codigo-aberto

Repositório intermediário do fluxo de espelhamento de código-fonte.

## Função

Recebe o código sanitizado do **gitlab-corporativo**, executa verificação de segredos e publica o resultado no **GitHub** após aprovação manual.

```
gitlab-corporativo → [este repositório] → GitHub
```

## Pipeline (`.gitlab-ci.yml`)

Disparada via trigger pelo gitlab-corporativo. Etapas:

| Stage | Job | Descrição |
|---|---|---|
| `fetch` | `baixar-artefato` | Baixa `codigo-sanitizado.tar.gz` via API REST |
| `scan` | `scan-gitleaks` | Scan de segredos |
| `update-branch` | `commit-codigo` | Aplica código sanitizado em branch e faz commit |
| `create-mr` | `criar-merge-request` | Abre MR para `main` via API |
| `push-github` | `espelhar-github` | *(Manual)* Push para o GitHub via PAT |

## Variáveis CI/CD necessárias

| Variável | Descrição |
|---|---|
| `GITLAB_CORP_URL` | URL base do gitlab corporativo |
| `GITLAB_CORP_PROJECT_ID` | Project id do api-regime-geral no gitlab corporativo |
| `GITLAB_CORP_TOKEN` | Token do gitlab corporativo com escopo ```read_api``` para baixar artefatos |
| `GIT_SERPRO_BOT_TOKEN` | Token do git.serpro com escopo ```write_repository``` |
| `GITHUB_REPO_URL` | URL HTTPS do repositório GitHub |
| `GITHUB_PAT` | PAT com escopo ```repo``` (push) |