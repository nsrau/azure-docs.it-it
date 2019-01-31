---
title: Configurare un nome di dominio personalizzato per l'account di archiviazione di Azure | Microsoft Docs
description: Usare il portale di Azure per eseguire il mapping del proprio nome canonico (CNAME) all'endpoint di archiviazione BLOB o Web in un account di archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: bbbb7f96ebf14781e2f461f5ffbb3e1172595928
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241288"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Configurare un nome di dominio personalizzato per l'account di archiviazione di Azure

È possibile configurare un nome di dominio personalizzato per l'accesso ai dati BLOB nell'account di archiviazione di Azure. L'endpoint predefinito per Archiviazione BLOB di Azure è *\<storage-account-name>.blob.core.windows.net*. È possibile anche usare l'endpoint Web generato come parte della [funzionalità di siti Web statici (anteprima)](storage-blob-static-website.md). Se si esegue il mapping di un dominio personalizzato e di un sottodominio come *www.contoso.com* all'endpoint BLOB o Web per l'account di archiviazione, gli utenti possono usare questo dominio per accedere ai dati BLOB dell'account di archiviazione.

> [!IMPORTANT]
> Archiviazione di Azure non supporta ancora in modo nativo HTTPS con domini personalizzati. Attualmente è possibile [usare la rete CDN di Azure per accedere ai BLOB usando domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Gli account di archiviazione attualmente supportano solo un nome di dominio personalizzato per ogni account. Non è possibile eseguire il mapping di un nome di dominio personalizzato agli endpoint sia del servizio BLOB che del servizio Web.

La tabella seguente riporta alcuni URL di esempio per i dati BLOB presenti in un account di archiviazione denominato *mystorageaccount*. Il dominio personalizzato registrato per l'account di archiviazione è *www.contoso.com*:

| Tipo di risorsa | URL predefinito | URL di dominio personalizzato |
| --- | --- | --- | --- |
| Account di archiviazione | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| BLOB |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Contenitore radice | http://mystorageaccount.blob.core.windows.net/myblob o http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob o http://www.contoso.com/$root/myblob |
| Web |  http://mystorageaccount.[zone].web.core.windows.net/$web/[indexdoc] or http://mystorageaccount.[zone].web.core.windows.net/[indexdoc] o http://mystorageaccount.[zone].web.core.windows.net/$web o http://mystorageaccount.[zone].web.core.windows.net/ | http://www.contoso.com/$web o http://www.contoso.com/ o http://www.contoso.com/$web/[indexdoc] o http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Come mostrato nelle sezioni seguenti, tutti gli esempi per l'endpoint del servizio BLOB si applicano anche all'endpoint del servizio Web.

## <a name="direct-vs-intermediary-domain-mapping"></a>Mapping di dominio diretto e con sottodominio intermedio

È possibile puntare il dominio personalizzato all'endpoint BLOB per l'account di archiviazione in uno dei due modi seguenti: 
* Usare il mapping diretto del record CNAME.
* Usare il sottodominio intermedio *asverify*.

### <a name="direct-cname-mapping"></a>Mapping diretto del record CNAME

Il primo e più semplice metodo consiste nel creare un record di nome canonico (CNAME) che esegue il mapping del dominio personalizzato e del sottodominio direttamente all'endpoint BLOB. Un record CNAME è una funzionalità Domain Name System (DNS) tramite cui viene eseguito il mapping di un dominio di origine a uno di destinazione. In questo esempio il dominio di origine è il proprio dominio personalizzato e sottodominio, ad esempio *www.contoso.com*. Il dominio di destinazione è l'endpoint del servizio BLOB, ad esempio *mystorageaccount.blob.core.windows.net*.

Il metodo diretto è descritto nella sezione "Registrare un dominio personalizzato".

### <a name="intermediary-mapping-with-asverify"></a>Mapping intermedio con *asverify*

Anche il secondo metodo usa i record CNAME. Per evitare tempi di inattività, tuttavia, prima viene usato un sottodominio speciale *asverify* riconosciuto da Azure.

Il mapping del dominio personalizzato a un endpoint BLOB può generare un breve periodo di inattività in fase di registrazione del dominio nel [portale di Azure](https://portal.azure.com). Se attualmente il dominio supporta un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività, usare il sottodominio *asverify* di Azure come passaggio di registrazione intermedio. Questo passaggio consente agli utenti di accedere al dominio durante il mapping DNS.

Informazioni sul metodo basato sul sottodominio intermedio sono disponibili in [Registrare un dominio personalizzato usando il sottodominio *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registrare un dominio personalizzato
Registrare il dominio usando la procedura descritta in questa sezione se si applicano le affermazioni seguenti:
* La non disponibilità temporanea del dominio agli utenti non è motivo di preoccupazione.
* Il dominio personalizzato non ospita attualmente un'applicazione. 

È possibile usare DNS di Azure per configurare un nome DNS personalizzato per l'archivio BLOB di Azure. Per altre informazioni, vedere [Usare il servizio DNS di Azure per specificare impostazioni di dominio personalizzate per un servizio di Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Se il dominio personalizzato supporta attualmente un'applicazione per cui non sono consentiti tempi di inattività, seguire la procedura descritta in [Registrare un dominio personalizzato usando il sottodominio *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

Per configurare un nome di dominio personalizzato, creare un nuovo record CNAME nel sistema DNS. Il record CNAME specifica un alias per un nome di dominio. In questo esempio viene eseguito il mapping dell'indirizzo del dominio personalizzato all'endpoint di archiviazione BLOB dell'account di archiviazione.

In genere è possibile gestire le impostazioni DNS del dominio sul sito Web del registrar del dominio. Ogni registrar prevede un metodo simile ma leggermente diverso per specificare un record CNAME. Il concetto di base è tuttavia identico. Poiché alcuni pacchetti di base di registrazione dei domini non offrono la configurazione DNS, potrebbe essere necessario aggiornare il pacchetto prima di creare il record CNAME.

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

1. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  
   Verrà visualizzato il riquadro **Dominio personalizzato**.

1. Accedere al sito Web del registrar di dominio e passare quindi alla pagina di gestione DNS,  
   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

1. Individuare la sezione per la gestione dei record CNAME.  
   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

1. Creare un nuovo record CNAME, immettere un alias di sottodominio, ad esempio **www** oppure **photos** e quindi specificare un nome host.  
   Il nome host è l'endpoint del servizio BLOB. Il formato è *\<mystorageaccount >.blob.core.windows.net*, dove *mystorageaccount* è il nome dell'account di archiviazione. Il nome host da usare viene visualizzato nell'elemento n. 1 del riquadro **Dominio personalizzato** nel [portale di Azure](https://portal.azure.com).

1. Nella casella di testo del riquadro **Dominio personalizzato** immettere il nome del dominio personalizzato, incluso il sottodominio.  
   Ad esempio, se il dominio è *contoso.com* e l'alias di sottodominio è *www*, immettere **www.contoso.com**. Se il sottodominio è *photos* immettere **photos.contoso.com**.

1. Per registrare il dominio personalizzato, selezionare **Salva**.  
   Se la registrazione ha esito positivo, una notifica nel portale informa che l'account di archiviazione è stato aggiornato correttamente.

Dopo che il nuovo record CNAME è stato propagato tramite DNS, se gli utenti dispongono delle autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Registrare un dominio personalizzato usando il sottodominio *asverify*.
Se attualmente il dominio personalizzato supporta un'applicazione con un contratto di servizio in base al quale non sono consentiti tempi di inattività, registrare il dominio personalizzato seguendo la procedura descritta in questa sezione. Creando un record CNAME che punta da *asverify.\<subdomain>.\<customdomain>* ad *asverify.\<storageaccount>.blob.core.windows.net.blob.core.windows.net*, è possibile preregistrare il dominio con Azure. È quindi possibile creare un secondo record CNAME che punta da *subdomain>.\<.\<customdomain>* ad *\<storageaccount.blob.core.windows.net*; il traffico diretto al dominio personalizzato verrà indirizzato all'endpoint BLOB.

Il sottodominio *asverify* è un sottodominio speciale riconosciuto da Azure. Anteponendo *asverify* al proprio sottodominio, si consente ad Azure di riconoscere il dominio personalizzato senza dover modificare il record DNS per il dominio. Quando si modifica il record DNS per il dominio, verrà eseguito il mapping all'endpoint BLOB senza tempi di inattività.

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

1. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  
   Verrà visualizzato il riquadro **Dominio personalizzato**.

1. Accedere al sito Web del provider DNS e passare alla pagina di gestione DNS.  
   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

1. Individuare la sezione per la gestione dei record CNAME.  
   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

1. Creare un nuovo record CNAME e fornire un alias di sottodominio che include il sottodominio *asverify*, ad esempio **asverify.www** o **asverify.photos**, quindi fornire un nome host.  
   Il nome host è l'endpoint del servizio BLOB. Il formato è *asverify.\<mystorageaccount>.blob.core.windows.net*, dove *mystorageaccount* è il nome dell'account di archiviazione. Il nome host da usare viene visualizzato nell'elemento n. 2 del riquadro *Dominio personalizzato* nel [portale di Azure](https://portal.azure.com).

1. Nella casella di testo del riquadro **Dominio personalizzato** immettere il nome del dominio personalizzato, incluso il sottodominio.  
   Non includere *asverify*. Ad esempio, se il dominio è *contoso.com* e l'alias di sottodominio è *www*, immettere **www.contoso.com**. Se il sottodominio è *photos* immettere **photos.contoso.com**.

1. Selezionare la casella di controllo **Usa convalida CNAME indiretta**.

1. Per registrare il dominio personalizzato, selezionare **Salva**.  
   Se la registrazione ha esito positivo, una notifica nel portale informa che l'account di archiviazione è stato aggiornato correttamente. Il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non è ancora instradato all'account di archiviazione.

1. Tornare al sito Web del provider DNS e quindi creare un altro record CNAME tramite cui eseguire il mapping del sottodominio all'endpoint del servizio BLOB.  
   Specificare ad esempio il sottodominio *www* o *photos* (senza *asverify*) e il nome host *\<mystorageaccount.blob.core.windows.net*, dove *mystorageaccount* è il nome dell'account di archiviazione. Con questo passaggio viene completata la registrazione del dominio personalizzato.

1. Infine è possibile eliminare il record CNAME creato contenente il sottodominio *asverify* poiché è necessario solo come passaggio intermedio.

Dopo che il nuovo record CNAME è stato propagato tramite DNS, se gli utenti dispongono delle autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

## <a name="test-your-custom-domain"></a>Testare un dominio personalizzato

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser, accedere al BLOB usando un URI nel formato seguente: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Per accedere a un modulo Web nel contenitore *myforms* nel sottodominio personalizzato *photos.contoso.com*, è possibile ad esempio usare l'URI seguente: `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Annullare la registrazione di un dominio personalizzato

Per annullare la registrazione di un dominio personalizzato per l'endpoint di archiviazione BLOB, usare una delle seguenti procedure.

### <a name="azure-portal"></a>Portale di Azure

Per rimuovere l'impostazione di dominio personalizzato, eseguire le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

1. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  
   Verrà visualizzato il riquadro **Dominio personalizzato**.

1. Cancellare il contenuto della casella di testo contenente il nome di dominio personalizzato.

1. Fare clic sul pulsante **Salva**.

Dopo aver rimosso correttamente il dominio personalizzato, verrà visualizzata una notifica nel portale che informa che l'account di archiviazione è stato aggiornato correttamente.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per rimuovere la registrazione di un dominio personalizzato, usare il comando [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) dell'interfaccia della riga di comando e quindi specificare una stringa vuota (`""`) per il valore dell'argomento `--custom-domain`.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per rimuovere la registrazione di un dominio personalizzato, usare il cmdlet di PowerShell [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e quindi specificare una stringa vuota (`""`) per il valore dell'argomento `-CustomDomainName`.

* Formato del comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Esempio del comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il mapping di un dominio personalizzato a un endpoint della rete per la distribuzione di contenuti (rete CDN) di Azure](../../cdn/cdn-map-content-to-custom-domain.md)
* [Usare Rete CDN di Azure per accedere ai BLOB usando domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md)
* [Hosting di siti Web statici in Archiviazione BLOB di Azure (anteprima)](storage-blob-static-website.md)
