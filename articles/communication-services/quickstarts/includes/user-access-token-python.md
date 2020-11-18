---
title: includere file
description: includere file
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: e307265cc95815f426317cee69d64b210bcd67a9
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506272"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 o versione successiva
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

1. Aprire la finestra del terminale o di comando per creare una nuova directory per l'app e passare a tale directory.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Usare un editor di testo per creare un file denominato **issue-access-tokens.py** nella directory radice del progetto e aggiungere la struttura per il programma, inclusa la gestione delle eccezioni di base. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Installare il pacchetto

Rimanendo nella directory dell'applicazione, installare la libreria client per gli SMS di Servizi di comunicazione di Azure per il pacchetto Python usando il comando `pip install`.

```console
pip install azure-communication-administration
```

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `CommunicationIdentityClient` con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Vedere come [gestire la stringa di connessione della risorsa](../create-communication-resource.md#store-your-connection-string).

Aggiungere questo codice all'interno del blocco `try`:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-an-identity"></a>Creare un'identità

Servizi di comunicazione di Azure gestisce una directory di identità leggera. Usare il metodo `create_user` per creare una nuova voce nella directory con `Id` univoco. Archiviare l'identità ricevuta con il mapping agli utenti dell'applicazione. Ad esempio, archiviarla nel database del server applicazioni. L'identità sarà necessaria in seguito per emettere i token di accesso.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier + ":")
```

## <a name="issue-access-tokens"></a>Risolvere i problemi dei token di accesso

Usare il metodo `issue_token` per emettere un token di accesso per un'identità esistente di Servizi di comunicazione. Il parametro `scopes` definisce il set di primitive che autorizzeranno questo token di accesso. Vedere l'[elenco delle azioni supportate](../../concepts/authentication.md). È possibile creare una nuova istanza del parametro `communicationUser` in base alla rappresentazione di stringa dell'identità di Servizi di comunicazione di Azure.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

I token di accesso sono credenziali di breve durata che devono essere riemesse. In caso contrario, si potrebbe verificare un'interruzione nell'esperienza degli utenti dell'applicazione. La proprietà della risposta `expires_on` indica la durata del token di accesso.

## <a name="refresh-access-tokens"></a>Aggiornare i token di accesso

Per aggiornare un token di accesso, usare l'oggetto `CommunicationUser` per riemetterlo:

```python  
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUser(existingIdentity)
token_result = client.issue_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Revocare i token di accesso

In alcuni casi, è possibile revocare esplicitamente i token di accesso, ad esempio quando l'utente di un'applicazione cambia la password che usa per l'autenticazione con il servizio. Il metodo `revoke_tokens` invalida tutti i token di accesso attivi che sono stati emessi all'identità.

```python  
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Eliminare un'identità

L'eliminazione di un'identità determina la revoca di tutti i token di accesso attivi e ne impedisce l'emissione per l'identità. Comporta inoltre la rimozione di tutto il contenuto persistente associato all'identità.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Eseguire il codice

Da un prompt della console passare alla directory contenente il file *issue-access-token.py* e quindi usare il comando `python` seguente per eseguire l'app.

```console
python ./issue-access-token.py
```
