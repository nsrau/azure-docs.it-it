---
title: Configurare un nome di dominio personalizzato per l&quot;endpoint dell&quot;archiviazione BLOB di Azure | Documentazione Microsoft
description: Usare il portale di Azure per eseguire il mapping del proprio nome canonico (CNAME) all&quot;endpoint di archiviazione BLOB in un account di archiviazione di Azure.
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e99294069f92f51d212b38b1c5ee12232c6dc77d
ms.lasthandoff: 04/27/2017


---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurare un nome di dominio personalizzato per l'endpoint di archiviazione BLOB

È possibile configurare un nome di dominio personalizzato per l'accesso ai dati BLOB nell'account di archiviazione di Azure. L'endpoint predefinito per l'archiviazione BLOB è `<storage-account-name>.blob.core.windows.net`. Se si esegue il mapping di un dominio personalizzato e di un sottodominio come **www.contoso.com** all'endpoint BLOB per l'account di archiviazione, anche gli utenti potranno accedere ai dati BLOB dell'account di archiviazione usando tale dominio.

> [!IMPORTANT]
> Archiviazione di Azure non supporta ancora HTTPS con domini personalizzati. Anche se non è ancora disponibile una sequenza temporale specifica da condividere, c'è interesse da parte dei clienti in questa funzionalità.
>

La tabella seguente riporta gli URL di esempio per i dati BLOB presenti in un account di archiviazione denominato **mystorageaccount**. Il dominio personalizzato registrato per l'account di archiviazione è **www.contoso.com**:

| Tipo di risorsa | URL predefinito | URL di dominio personalizzato |
| --- | --- | --- |
| Account di archiviazione | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| BLOB |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Contenitore radice | http://mystorageaccount.blob.core.windows.net/myblob or http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob or http://www.contoso.com/$root/myblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>Mapping di dominio diretto e con sottodominio intermedio

Esistono due modi per puntare il dominio personalizzato all'endpoint BLOB per l'account di archiviazione: mapping CNAME diretto e utilizzo del sottodominio intermedio *asverify*.

### <a name="direct-cname-mapping"></a>Mapping diretto del record CNAME

Il primo e più semplice metodo consiste nel creare un record di nome canonico (CNAME) che esegue il mapping del dominio personalizzato e del sottodominio direttamente all'endpoint BLOB. Un record CNAME è una funzionalità Domain Name System (DNS) tramite cui viene eseguito il mapping di un dominio di origine a uno di destinazione. In questo caso il dominio di origine è il proprio dominio personalizzato e sottodominio, ad esempio *www.contoso.com*. Il dominio di destinazione è l'endpoint del servizio BLOB, ad esempio *mystorageaccount.blob.core.windows.net*.

Il metodo diretto è descritto in [Registrare un dominio personalizzato](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Mapping intermedio con *asverify*

Anche il secondo metodo usa record CNAME ma prima impiega un sottodominio speciale riconosciuto da Azure per evitare tempi di inattività: **asverify**.

Il processo di mapping del dominio personalizzato a un endpoint di BLOB può tuttavia comportare un breve periodo di inattività del dominio durante la relativa registrazione nel [portale di Azure](https://portal.azure.com). Se il dominio personalizzato supporta attualmente un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività, è possibile usare il sottodominio *asverify* di Azure per fornire un passaggio di registrazione intermedio. Questo passaggio intermedio assicura che gli utenti possano accedere al dominio durante l'applicazione del mapping DNS.

Il metodo del sottodominio intermedio è trattato in [Registrare un dominio personalizzato usando il sottodominio *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registrare un dominio personalizzato
Eseguire questa procedura per registrare il dominio personalizzato se il fatto che il dominio sia temporaneamente non disponibile per gli utenti non costituisce un problema oppure se il dominio personalizzato al momento non ospita un'applicazione.

Se invece il dominio personalizzato supporta un'applicazione per cui non sono consentiti tempi di inattività, eseguire la procedura descritta in [Registrare un dominio personalizzato usando il sottodominio *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

Per configurare un nome di dominio personalizzato, è necessario creare un nuovo record CNAME nel DNS. Il record CNAME specifica un alias per un nome di dominio. In questo caso viene usato per eseguire il mapping dell'indirizzo del dominio personalizzato all'endpoint dell'archiviazione BLOB per l'account di archiviazione.

In genere è possibile gestire le impostazioni DNS del dominio sul sito Web del registrar del dominio. Ogni registrar prevede un metodo simile ma leggermente diverso per specificare un record CNAME. Il concetto di base è tuttavia identico. Alcuni pacchetti di base di registrazione dei domini non offrono la configurazione dei DNS, quindi può essere necessario aggiornare il pacchetto prima di creare il record CNAME.

1. Passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. In **SERVIZIO BLOB** nel pannello menu selezionare **Dominio personalizzato** per aprire il pannello *Dominio personalizzato*.
1. Accedere al sito Web del registrar di dominio e passare alla pagina di gestione dei DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
1. Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.
1. Creare un nuovo record CNAME e specificare un alias di sottodominio, ad esempio **www** o **photos**. Specificare quindi un nome host, che corrisponde all'endpoint di servizio BLOB, nel formato **mystorageaccount.blob.core.windows.net**, dove *mystorageaccount* è il nome dell'account di archiviazione. Il nome host da usare nell'elemento #1 è visualizzato del pannello *Dominio personalizzato* nel [portale di Azure](https://portal.azure.com).
1. Nella casella di testo del pannello *Dominio personalizzato* del [portale di Azure](https://portal.azure.com) immettere il nome del dominio personalizzato, incluso il sottodominio. Ad esempio, se il dominio è **contoso.com** e l'alias di sottodominio è **www**, immettere **www.contoso.com**. Se il sottodominio è **photos** immettere **photos.contoso.com**. Il sottodominio è *obbligatorio*.
1. Selezionare **Salva** nel pannello *Dominio personalizzato* per registrare il dominio personalizzato. Se la registrazione ha esito positivo verrà visualizzato un messaggio che informa che l'account di archiviazione è stato aggiornato correttamente.

Dopo che il nuovo record CNAME è stato propagato tramite DNS, gli utenti possono visualizzare i dati BLOB usando il dominio personalizzato, purché dispongano delle autorizzazioni appropriate.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Registrare un dominio personalizzato usando il sottodominio *asverify*
Usare questa procedura per registrare un dominio personalizzato che attualmente supporta un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività. Creando un record CNAME che punti da `asverify.<subdomain>.<customdomain>` a `asverify.<storageaccount>.blob.core.windows.net` è possibile preregistrare il dominio con Azure. È quindi possibile creare un secondo record CNAME che punta da `<subdomain>.<customdomain>` a `<storageaccount>.blob.core.windows.net`. A questo punto il traffico diretto al dominio personalizzato verrà indirizzato all'endpoint BLOB.

Il sottodominio **asverify** è un sottodominio speciale riconosciuto da Azure. Anteponendo `asverify` al proprio sottodominio, si consente ad Azure di riconoscere il dominio personalizzato senza modificare il relativo record DNS. Quando si modifica il record DNS per il dominio, verrà eseguito il mapping all'endpoint BLOB senza tempi di inattività.

1. Passare all'account di archiviazione nel [portale di Azure](https://portal.azure.com).
1. In **SERVIZIO BLOB** nel pannello menu selezionare **Dominio personalizzato** per aprire il pannello *Dominio personalizzato*.
1. Accedere al sito Web del provider DNS e passare alla pagina di gestione dei DNS. Queste informazioni possono essere disponibili in una sezione come **Domain Name**, **DNS** o **Name Server Management**.
1. Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.
1. Creare un nuovo record CNAME e fornire un alias di sottodominio che include il sottodominio *asverify*. Ad esempio **asverify.www** o **asverify.photos**. Specificare quindi un nome host, che corrisponde all'endpoint di servizio BLOB, nel formato **asverify.mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Il nome host da usare viene visualizzato nell'elemento #2 del pannello *Dominio personalizzato* nel [portale di Azure](https://portal.azure.com).
1. Nella casella di testo del pannello *Dominio personalizzato* del [portale di Azure](https://portal.azure.com) immettere il nome del dominio personalizzato, incluso il sottodominio. Non includere *asverify*. Ad esempio, se il dominio è **contoso.com** e l'alias di sottodominio è **www**, immettere **www.contoso.com**. Se il sottodominio è **photos** immettere **photos.contoso.com**. Il sottodominio è obbligatorio.
1. Selezionare la casella di controllo **Usa convalida CNAME indiretta**.
1. Selezionare **Salva** nel pannello *Dominio personalizzato* per registrare il dominio personalizzato. Se la registrazione ha esito positivo verrà visualizzato un messaggio che informa che l'account di archiviazione è stato aggiornato correttamente. A questo punto, il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non è ancora instradato all'account di archiviazione.
1. Tornare al sito Web del provider DNS e creare un altro record CNAME tramite cui eseguire il mapping del sottodominio all'endpoint del servizio BLOB. Specificare ad esempio il sottodominio **www** o **photos** (senza *asverify*) e il nome host **mystorageaccount.blob.core.windows.net**, dove **mystorageaccount** è il nome dell'account di archiviazione. Con questo passaggio viene completata la registrazione del dominio personalizzato.
1. Infine è possibile eliminare il record CNAME creato contenente il sottodominio **asverify**, poiché è stato necessario solo come passaggio intermedio.

Dopo che il nuovo record CNAME è stato propagato tramite DNS, gli utenti possono visualizzare i dati BLOB usando il dominio personalizzato, purché dispongano delle autorizzazioni appropriate.

## <a name="test-your-custom-domain"></a>Testare un dominio personalizzato

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato effettivamente eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser utilizzare un URI nel formato seguente per accedere al BLOB:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

È possibile ad esempio usare l'URI seguente per accedere a un Web Form nel contenitore **myforms** nel sottodominio personalizzato **photos.contoso.com**:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Annullare la registrazione di un dominio personalizzato

Per annullare la registrazione di un dominio personalizzato per l'endpoint di archiviazione BLOB, usare una delle seguenti procedure.

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

Usare il comando [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#update) dell'interfaccia della riga di comando e specificare una stringa vuota (`""`) per il valore dell'argomento `--custom-domain` per rimuovere la registrazione di un dominio personalizzato.

* Formato del comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Esempio del comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

Usare il cmdlet di PowerShell [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) e specificare una stringa vuota (`""`) per il valore dell'argomento `-CustomDomainName` per rimuovere la registrazione di un dominio personalizzato.

* Formato del comando:
  
  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Esempio del comando:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

### <a name="azure-portal"></a>Portale di Azure

Attualmente non è possibile rimuovere la registrazione di un dominio personalizzato usando il portale di Azure. Questo è un problema noto. Attualmente non è disponibile una data di risoluzione, ma questo articolo verrà aggiornato dopo la risoluzione del problema. Nel frattempo usare l'interfaccia della riga di comando di Azure 2.0 o Azure PowerShell per rimuovere la configurazione del dominio personalizzato.

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il mapping di un dominio personalizzato a un endpoint della rete per la distribuzione di contenuti (rete CDN) di Azure](../cdn/cdn-map-content-to-custom-domain.md)


