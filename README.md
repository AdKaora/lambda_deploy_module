# Deploy to Lambda Workflow

Questo repository contiene un workflow riutilizzabile per GitHub Actions che facilita il deploy di funzioni AWS Lambda. Utilizzando questo workflow, puoi automatizzare il processo di aggiornamento del codice delle tue funzioni Lambda direttamente dal tuo repository GitHub.

## Sommario

- [Prerequisiti](#prerequisiti)
- [Inputs](#inputs)
- [Secrets](#secrets)
- [Come Utilizzare il Workflow](#come-utilizzare-il-workflow)

## Prerequisiti

Prima di utilizzare questo workflow, assicurati di avere:

1. **Una funzione Lambda** esistente che desideri aggiornare.

## Inputs

Il workflow richiede i seguenti input per funzionare correttamente:

| Nome        | Tipo   | Obbligatorio | Descrizione                                  |
|-------------|--------|--------------|----------------------------------------------|
| `FILE_NAME` | string | Sì           | Nome del file contenente il codice da deployare (con estensione). |
| `LAMBDA_NAME` | string | Sì         | Nome della funzione Lambda da aggiornare (esistente).     |
| `AWS_REGION` | string | Sì          | Regione AWS in cui si trova la funzione Lambda. |

## Secrets

Il workflow utilizza i seguenti segreti per garantire la sicurezza delle credenziali:

| Nome           | Descrizione                                         |
|----------------|-----------------------------------------------------|
| `AWS_ROLE_ARN` | ARN del ruolo IAM da assumere per eseguire le operazioni AWS. |

Assicurati di configurare questo segreto nel tuo repository GitHub:

1. Vai alle **Impostazioni** del tuo repository.
2. Seleziona **Secrets and variables** > **Actions**.
3. Clicca su **New repository secret**.
4. Aggiungi `AWS_ROLE_ARN` con il valore corrispondente.

## Come Utilizzare il Workflow

Per utilizzare questo workflow riutilizzabile nel tuo repository, devi richiamarlo da un altro workflow. Di seguito sono riportati i passaggi dettagliati su come configurarlo.
Crea un file di workflow nel tuo repository (ad esempio `.github/workflows/deploy.yml`) con il seguente contenuto:

```yaml
name: Main Workflow

on:
  push:
    branches:
      - master
      
permissions:
  id-token: write      # Permesso necessario per OIDC
  contents: read

jobs:
  deploy_lambda:
    uses: AdKaora/lambda_deploy_module/.github/workflows/deploy_lambda.yml@master
    with:
      FILE_NAME: notifier_github_to_slack.py
      LAMBDA_NAME: notifier_github_to_slack
      AWS_REGION: eu-west-1
    secrets:
      AWS_ROLE_ARN: ${{ secrets.AWS_ROLE_ARN }}
```

#### Spiegazione dell'Esempio

- **name**: Nome del workflow.
- **on**: Evento che triggera il workflow (in questo caso, un push sul branch `main`).
- **jobs**: Definizione dei job da eseguire.
  - **deploy-lambda**: Nome del job che utilizza il workflow riutilizzabile.
    - **uses**: Percorso al workflow riutilizzabile.
    - **with**: Passaggio degli input richiesti.
      - `FILE_NAME`: Nome del file contenente il codice Lambda.
      - `LAMBDA_NAME`: Nome della funzione Lambda da aggiornare.
      - `AWS_REGION`: Regione AWS.
    - **secrets**: Passaggio del segreto `AWS_ROLE_ARN`.