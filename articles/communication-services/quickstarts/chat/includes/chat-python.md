---
title: includere file
description: includere file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523777"
---
## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, assicurarsi di:

- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installare [Python](https://www.python.org/downloads/)
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di comunicazione di Azure](../../create-communication-resource.md). Per questa guida di avvio rapido sarà necessario registrare l'**endpoint** della risorsa.
- Un [token di accesso utente](../../access-tokens.md). Assicurarsi di impostare l'ambito su "chat" e prendere nota della stringa del token, nonché della stringa userId.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Aprire il terminale o la finestra di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Usare un editor di testo per creare un file denominato **start-chat.py** nella directory radice del progetto e aggiungere la struttura per il programma, inclusa la gestione delle eccezioni di base. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Installare la libreria client

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client di chat di Servizi di comunicazione di Azure per Python.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Questa classe è necessaria per la funzionalità di chat. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per creare, ottenere ed eliminare thread. |
| ChatThreadClient | Questa classe è necessaria per la funzionalità di thread di chat. È possibile ottenere un'istanza tramite ChatClient e usarla per inviare/ricevere/aggiornare/eliminare messaggi, aggiungere/rimuovere/ottenere utenti, inviare notifiche di digitazione in corso e conferme di lettura. |

## <a name="create-a-chat-client"></a>Creare un client di chat

Per creare un client di chat, si useranno l''endpoint di Servizi di comunicazione e il `Access Token` generato nell'ambito dei passaggi preliminari. Vedere altre informazioni sui [token di accesso utente](../../access-tokens.md).

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Avviare un thread di chat

Usare il metodo `create_chat_thread` per creare un thread di chat.

- Usare `topic` per specificare un argomento per il thread. L'argomento può essere aggiornato dopo la creazione del thread di chat usando la funzione `update_thread`.
- Usare `members` per elencare il `ChatThreadMember` da aggiungere al thread di chat. `ChatThreadMember` accetta il tipo `CommunicationUser` come `user`, che corrisponde a quello ottenuto in [Creare un utente](../../access-tokens.md#create-an-identity).

Il `chat_thread_client` di risposta viene usato per eseguire operazioni sul thread di chat creato: aggiunta di membri al thread di chat, invio di un messaggio, eliminazione di un messaggio e così via. Contiene una proprietà `thread_id` che rappresenta l'ID univoco del thread di chat.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Ottenere un client di thread di chat
Il metodo get_chat_thread_client restituisce un client di thread per un thread già esistente. Può essere usato per eseguire operazioni sul thread creato: aggiungere membri, inviare messaggi e così via. thread_id è l'ID univoco del thread di chat esistente.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Inviare un messaggio a un thread di chat

Usare il metodo `send_message` per inviare un messaggio al thread di chat appena creato, identificato da threadId.

- Usare `content` per fornire il contenuto del messaggio di chat.
- Usare `priority` per specificare il livello di priorità del messaggio, ad esempio 'Normal' o 'High'. Questa proprietà può essere usata per avere un indicatore dell'interfaccia utente per l'utente destinatario nell'app, per attirare l'attenzione sul messaggio o eseguire logica di business personalizzata.
- Usare `senderDisplayName` per specificare il nome visualizzato del mittente.

Il `SendChatMessageResult` di risposta contiene un "id", ovvero l'ID univoco del messaggio.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Ricevere messaggi di chat da un thread di chat

È possibile recuperare i messaggi di chat eseguendo il polling del metodo `list_messages` a intervalli specificati.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` restituisce la versione più recente del messaggio, incluse eventuali modifiche o eliminazioni che si sono verificate nel messaggio usando `update_message` e `delete_message`. Per i messaggi eliminati, `ChatMessage.deleted_on` restituisce un valore datetime che indica quando il messaggio è stato eliminato. Per i messaggi modificati, `ChatMessage.edited_on` restituisce un valore datetime che indica quando il messaggio è stato modificato. È possibile accedere all'ora originale di creazione del messaggio usando `ChatMessage.created_on` e tale ora può essere usata per ordinare i messaggi.

`list_messages` restituisce tipi diversi di messaggi che possono essere identificati da `ChatMessage.type`. Questi tipi sono:

- `Text`: messaggio di chat normale inviato da un membro del thread.

- `ThreadActivity/TopicUpdate`: messaggio di sistema che indica che l'argomento è stato aggiornato.

- `ThreadActivity/AddMember`: messaggio di sistema che indica che uno o più membri sono stati aggiunti al thread di chat.

- `ThreadActivity/DeleteMember`: messaggio di sistema che indica che un membro è stato rimosso dal thread di chat.

Per altri dettagli, vedere [Tipi di messaggi](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Aggiungere un utente come membro al thread di chat

Dopo aver creato un thread di chat, è possibile aggiungere e rimuovere utenti. Aggiungendo utenti, si concede loro l'accesso per inviare messaggi al thread di chat e aggiungere o rimuovere altri membri. Prima di chiamare il metodo `add_members`, verificare di avere acquisito un nuovo token di accesso e un'identità per tale utente. L'utente dovrà usare il token di accesso per inizializzare il client di chat.

Usare il metodo `add_members` per aggiungere membri di thread al thread identificato da threadId.

- Usare `members` per elencare i membri da aggiungere al thread di chat.
- `user`, obbligatoria, è il `CommunicationUser` creato da `CommunicationIdentityClient` in [Creare un utente](../../access-tokens.md#create-an-identity)
- `display_name`, facoltativa, è il nome visualizzato per il membro del thread.
- `share_history_time`, facoltativa, è l'ora da cui la cronologia della chat viene condivisa con il membro. Per condividere la cronologia dall'inizio del thread di chat, impostare questa proprietà su qualsiasi data uguale o precedente all'ora di creazione del thread. Per non condividere alcuna cronologia precedente all'aggiunta del membro, impostarla sulla data corrente. Per condividere una cronologia parziale, impostarla su una data intermedia.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Rimuovere un utente da un thread di chat

Analogamente all'aggiunta di un membro, è anche possibile rimuovere membri da un thread. Per eseguire la rimozione, è necessario tenere traccia degli ID dei membri aggiunti.

Usare il metodo `remove_member` per rimuovere membri di thread dal thread identificato da threadId.
- `user` è il CommunicationUser da rimuovere dal thread.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `python`.

```console
python start-chat.py
```
