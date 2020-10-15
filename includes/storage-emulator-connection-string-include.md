---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87070551"
---
Azzurrite supporta un singolo account fisso e una chiave di autenticazione nota per l'autenticazione con chiave condivisa. Questo account e questa chiave sono le uniche credenziali chiave condivise consentite per l'uso con azzurrite. ovvero:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> La chiave di autenticazione supportata da azzurrite è destinata solo a testare la funzionalità del codice di autenticazione client. Non viene utilizzata per eventuali scopi di sicurezza. Non è possibile usare l'account e la chiave di archiviazione di produzione con azzurrite. Non usare l'account di sviluppo con dati di produzione.
> 
> Azzurrite supporta solo la connessione tramite HTTP. HTTPS è tuttavia il protocollo consigliato per accedere alle risorse in un account di archiviazione di Azure di produzione.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Connettersi all'account dell'emulatore utilizzando un collegamento
Il modo più semplice per connettersi a azzurrite dall'applicazione consiste nel configurare una stringa di connessione nel file di configurazione dell'applicazione che fa riferimento al collegamento `UseDevelopmentStorage=true` . Di seguito è riportato un esempio di una stringa di connessione per azzurrite in un file di *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Connettersi all'account dell'emulatore utilizzando il nome account ben noto e la chiave
Per creare una stringa di connessione che fa riferimento al nome e alla chiave dell'account emulatore, è necessario specificare gli endpoint per ciascuno dei servizi che si vogliono usare dall'emulatore nella stringa di connessione. Ciò è necessario per fare in modo che la stringa di connessione faccia riferimento agli endpoint dell’emulatore, che variano rispetto a quelli per un account di archiviazione di produzione. Ad esempio, il valore della stringa di connessione risulterà simile al seguente:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Questo valore è identico al collegamento mostrato in precedenza, `UseDevelopmentStorage=true`.
