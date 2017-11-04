---
title: Integrazione di Azure Data Center di Stack - DNS
description: Informazioni su come integrare Azure Stack DNS con il Data Center DNS
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/10/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 40d6d4858ef2e3df61d04dc68c00e09c04f000e2
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integrazione di Azure Data Center di Stack - DNS

*Si applica a: Azure Stack integrate di sistemi*

Per poter accedere agli endpoint di Azure Stack (`portal`, `adminportal`, `management`, `adminmanagement`, ecc.)  dallo Stack all'esterno di Azure, è necessario integrare i servizi di Azure Stack DNS con i server DNS che ospitano le zone DNS che si desidera utilizzare nello Stack di Azure.

## <a name="azure-stack-dns-namespace"></a>Spazio dei nomi DNS di Stack di Azure
Verrà richiesto di fornire alcune informazioni importanti relative a DNS quando si distribuisce Azure Stack.


|Campo  |Descrizione  |Esempio|
|---------|---------|---------|
|Region|La posizione geografica della distribuzione di Azure Stack.|`east`|
|Nome di dominio esterno|Il nome della zona di cui che si desidera utilizzare per la distribuzione di Azure Stack.|`cloud.fabrikam.com`|
|Nome di dominio interno|Il nome dell'area di lavoro interno utilizzato per i servizi di infrastruttura nello Stack di Azure.  È integrato al servizio Directory e privati (non raggiungibile di fuori della distribuzione di Azure Stack).|`azurestack.local`|
|Server d'inoltro DNS|Server DNS utilizzati per inoltrare query DNS e zone DNS i record che sono ospitati all'esterno dello Stack di Azure, sia nella rete intranet aziendale o in internet pubblica.|`10.57.175.34`<br>`8.8.8.8`|
|Prefisso di denominazione (facoltativo)|Il prefisso di denominazione. che dai nomi di computer Azure Stack infrastruttura ruolo istanza.  Se omesso, il valore predefinito è `azs`.|`azs`|

Il nome di dominio completo (FQDN) della distribuzione di Azure Stack e l'endpoint è la combinazione del parametro regione e il parametro di nome di dominio esterno. Utilizzando i valori degli esempi nella tabella precedente, il nome di dominio completo per la distribuzione di Azure Stack è il nome seguente:

`east.cloud.fabrikam.com`

Di conseguenza, alcuni esempi degli endpoint per la distribuzione deve essere simile gli URL seguenti:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Per utilizzare questo spazio dei nomi DNS di esempio per una distribuzione di Stack di Azure, sono necessarie le seguenti condizioni:

- La zona `fabrikam.com` è registrato con un registrar di dominio, un server DNS azienda interno o entrambi, a seconda dei requisiti di risoluzione del nome.
- Il dominio figlio `cloud.fabrikam.com` esiste nella zona `fabrikam.com`.
- I server DNS che ospitano le zone `fabrikam.com` e `cloud.fabrikam.com` sia raggiungibile dalla distribuzione dello Stack di Azure.

Per essere in grado di risolvere nomi DNS per l'endpoint di Azure Stack e le istanze dallo Stack all'esterno di Azure, è necessario integrare il server DNS che ospitano la zona DNS esterna per lo Stack di Azure con i server DNS che ospitano la zona padre che si desidera utilizzare.


## <a name="resolution-and-delegation"></a>Risoluzione e delega

Esistono due tipi di server DNS:

- Un server DNS autorevole ospita le zone DNS. e risponde alle query DNS solo per i record presenti in tali zone.
- Un server DNS ricorsivo non ospita le zone DNS. ma risponde a tutte le query DNS, chiamando i server DNS autorevoli per raccogliere tutti i dati necessari.

Stack di Azure include sia attendibile e i server DNS ricorsivo. Il server ricorsiva vengono utilizzati per risolvere i nomi di tutti gli elementi tranne la zona privata interna e la zona DNS esterna pubblica per la distribuzione di Azure Stack. 

![Architettura dello Stack DNS di Azure](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Risoluzione dei nomi DNS esterni dallo Stack di Azure

Per risolvere i nomi DNS per gli endpoint all'esterno dello Stack di Azure (ad esempio: www.bing.com), è necessario fornire i server DNS che Azure Stack può utilizzare per inoltrare le richieste DNS per il quale non è autorevole dello Stack di Azure. Per la distribuzione, sono necessari server DNS che Azure Stack inoltra le richieste per il foglio di lavoro di distribuzione (nel campo server d'inoltro DNS). Specificare almeno due server in questo campo per la tolleranza di errore. Senza questi valori, la distribuzione dello Stack di Azure non riesce.

### <a name="configure-conditional-dns-forwarding"></a>Configurare l'inoltro condizionale di DNS

> [!IMPORTANT]
> Si applica solo a una distribuzione di ADFS.

Per abilitare la risoluzione dei nomi con l'infrastruttura DNS esistente, configurare l'inoltro condizionale.

Per aggiungere un server d'inoltro condizionale, è necessario utilizzare l'endpoint con privilegi.

Per questa procedura, utilizzare un computer della rete di Data Center in grado di comunicare con l'endpoint con privilegi nello Stack di Azure.

1. Aprire una sessione di Windows PowerShell con privilegi elevata (Esegui come amministratore) e connettersi all'indirizzo IP dell'endpoint con privilegi. Utilizzare le credenziali per l'autenticazione CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Dopo la connessione all'endpoint con privilegi, eseguire il comando PowerShell seguente. Sostituire i valori di esempio forniti con il nome di dominio e gli indirizzi IP dei server DNS che si desidera utilizzare.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Risoluzione dei nomi DNS di Azure Stack dallo Stack all'esterno di Azure
I server autorevoli sono quelle che contengono le informazioni di zona DNS esterne e tutte le zone creati dall'utente. Integrazione con questi server per abilitare la delega di zona o l'inoltro condizionale per risolvere i nomi DNS di Azure Stack dallo Stack all'esterno di Azure.

## <a name="get-dns-server-external-endpoint-information"></a>Ottenere informazioni sull'endpoint esterni di Server DNS

Per integrare la distribuzione di Azure Stack con l'infrastruttura DNS, è necessario le informazioni seguenti:

- Server DNS FQDN
- Indirizzi IP del server DNS

I nomi FQDN per i server DNS di Azure Stack hanno il seguente formato:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Usando i valori di esempio, il FQDN per il DNS Server sono:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Questa informazione viene anche creata alla fine di tutte le distribuzioni di Azure Stack in un file denominato `AzureStackStampDeploymentInfo.json`. Questo file si trova nel `C:\CloudDeployment\logs` cartella della macchina virtuale di distribuzione. Se non si è certi i valori utilizzati per la distribuzione dello Stack di Azure, è possibile ottenere i valori da qui.

Se la macchina virtuale di distribuzione non è più disponibile o non è accessibile, è possibile ottenere i valori per la connessione all'endpoint con privilegi e l'esecuzione di `Get-AzureStackInfo` cmdlet di PowerShell. Per ulteriori informazioni relative all'endpoint con privilegi, vedere (insert) sul collegamento per l'articolo qui.

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Configurazione di inoltro condizionale allo Stack di Azure

Il modo più semplice e sicuro per integrare Azure Stack con l'infrastruttura DNS consiste nell'eseguire l'inoltro condizionale della zona dal server che ospita la zona padre. Questo approccio è consigliato se si ha controllo diretto su server DNS che ospitano la zona padre allo spazio dei nomi DNS esterni dello Stack di Azure.

Se non si ha familiarità con le procedure per eseguire l'inoltro condizionale con DNS, vedere l'articolo di TechNet: [assegnare un server d'inoltro condizionale per un nome di dominio](https://technet.microsoft.com/library/cc794735), o la documentazione specifica per la soluzione DNS.

In scenari in cui è specificata la zona DNS di Stack Azure esterno come un dominio figlio del nome del dominio aziendale, inoltro condizionale non può essere utilizzato. È necessario configurare la delega DNS.

Esempio:

- Nome di dominio DNS aziendale:`contoso.com`
- Nome di dominio DNS esterno dello Stack di Azure:`azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delega la zona DNS esterna allo Stack di Azure

Per i nomi DNS essere risolto all'esterno di una distribuzione di Stack di Azure, è necessario configurare la delega DNS.

Ogni registrar prevede i propri strumenti di gestione DNS per modificare i record del server dei nomi per un dominio. Nella pagina di gestione DNS del registrar, modificare i record NS e sostituire i record NS per la zona con quelli nello Stack di Azure.

La maggior parte dei registri DNS è necessario fornire un minimo di due server DNS per completare la delega.

## <a name="next-steps"></a>Passaggi successivi

[Integrazione di Azure Data Center di Stack - identità](azure-stack-integrate-identity.md)
