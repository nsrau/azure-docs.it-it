---
title: "Guida introduttiva: Creare un'app Python che usa Cache Redis di Azure | Microsoft Docs"
description: In questa guida introduttiva viene illustrato come creare un'app Python che usa Cache Redis di Azure
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/11/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: fcb5d74305375c9883f2d601a2ab7d2d9a50b9cb
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743941"
---
# <a name="quickstart-use-azure-cache-for-redis-with-python"></a>Guida introduttiva: Usare Cache Redis di Azure con Python

In questa guida di avvio rapido si incorpora Azure Cache for Redis in un'app Python per accedere a una cache sicura e dedicata accessibile da qualsiasi applicazione in Azure.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Python 2 o 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Creare una cache di Azure per Redis in Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Installare redis-py

[Redis py](https://github.com/andymccurdy/redis-py) è un'interfaccia di Python per Cache Redis di Azure. Usare lo strumento per i pacchetti Python *pip* per installare il pacchetto redis-py. 

L'esempio seguente usa *pip3* per Python3 per installare il pacchetto redis-py in Windows 10 usando un prompt dei comandi per gli sviluppatori di Visual Studio 2019 con privilegi di amministratore elevati.

```python
    pip3 install redis
```

![Installare redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Leggere e scrivere nella cache

Eseguire Python e testare l'uso della cache dalla riga di comando. Sostituire `<Your Host Name>` e `<Your Access Key>` con i valori per l'istanza di Cache Redis di Azure. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Per Redis versione 3.0 o successive, il controllo del certificato SSL è obbligatorio. ssl_ca_certs deve essere impostato in modo esplicito durante la connessione a Redis. Nel caso di RH Linux, ssl_ca_certs è reperibile nel modulo certificato "/etc/pki/tls/certs/ca-bundle.crt".

## <a name="create-a-python-script"></a>Creare uno script Python

Creare un nuovo file di testo di script denominato *PythonApplication1.py*.

Aggiungere lo script seguente in *PythonApplication1.py* e salvare il file. Questo script eseguirà il test di accesso alla cache. Sostituire `<Your Host Name>` e `<Your Access Key>` con i valori per l'istanza di Cache Redis di Azure. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Eseguire lo script con Python.

![Test Python completato](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di proseguire con un'altra esercitazione, è possibile conservare le risorse create in questa guida introduttiva e riutilizzarle.

In caso contrario, se si è terminato il lavoro con l'applicazione di esempio di avvio rapido, è possibile eliminare le risorse di Azure create in questo avvio rapido per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.
>

Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse. Le istruzioni di questo articolo usano un gruppo di risorse denominato *TestResources*. Nel gruppo di risorse nell'elenco dei risultati selezionare **...** e quindi **Elimina gruppo di risorse**.

![Delete](./media/cache-web-app-howto/cache-delete-resource-group.png)

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e selezionare **Elimina**.

Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una semplice app Web ASP.NET che usa Cache Redis di Azure.](./cache-web-app-howto.md)

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
