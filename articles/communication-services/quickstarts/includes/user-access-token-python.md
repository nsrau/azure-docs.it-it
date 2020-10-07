---
title: includere file
description: includere file
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944584"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 o versione successiva
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

1. Aprire la finestra del terminale o di comando per creare una nuova directory per l'app e passare a tale directory.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Usare un editor di testo per creare un file denominato **issue-tokens.py** nella directory radice del progetto e aggiungere la struttura per il programma, inclusa la gestione delle eccezioni di base. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
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

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

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

## <a name="create-a-user"></a>Creare un utente

Servizi di comunicazione di Azure gestisce una directory di identità leggera. Usare il metodo `create_user` per creare una nuova voce nella directory con `Id` univoco. È necessario mantenere un mapping tra gli utenti dell'applicazione e le identità generate da Servizi di comunicazione, ad esempio archiviandoli nel database del server applicazioni.

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Emettere i token di accesso utente

Usare il metodo `issue_token` per emettere un token di accesso per un utente di Servizi di comunicazione. Se non viene fornito il parametro `user` facoltativo, verrà creato un nuovo utente e restituito con il token.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

I token di accesso utente sono credenziali di breve durata che devono essere riemesse per impedire che si verifichino interruzioni del servizio per gli utenti. La proprietà di risposta `expires_on` indica la durata del token.

## <a name="revoke-user-access-tokens"></a>Revocare i token di accesso utente

In alcuni casi, può essere necessario revocare i token di accesso utente in modo esplicito, ad esempio quando un utente modifica la password usata per eseguire l'autenticazione al servizio. Questa funzionalità è disponibile tramite la libreria client di amministrazione di Servizi di comunicazione di Azure.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Eliminare un utente

L'eliminazione di un'identità determina la revoca di tutti i token attivi e impedisce l'emissione di token successivi per le identità. Viene anche rimosso tutto il contenuto persistente associato all'utente.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>Eseguire il codice

Da un prompt della console passare alla directory contenente il file *issue-token.py* e quindi usare il comando `python` seguente per eseguire l'app.

```console
python ./issue-token.py
```
