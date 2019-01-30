---
title: Integrazione di Data Center Azure Stack - DNS
description: Descrive come integrare DNS di Azure Stack con il tuo Data Center DNS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
keywords: ''
ms.openlocfilehash: dce59cf012a8c3bc5b6b3baf705b5619bdf43104
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252312"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integrazione di Data Center Azure Stack - DNS
Per poter accedere agli endpoint di Azure Stack (**portal**, **adminportal**, **gestione**, **adminmanagement**e così via.)  dall'esterno di Azure Stack, è necessario integrare i servizi DNS di Azure Stack con il server DNS che ospitano le zone DNS da usare in Azure Stack.

## <a name="azure-stack-dns-namespace"></a>Spazio dei nomi DNS di Stack di Azure
Viene richiesto di fornire alcune informazioni importanti relative a DNS durante la distribuzione di Azure Stack.


|Campo  |DESCRIZIONE  |Esempio|
|---------|---------|---------|
|Region|La posizione geografica di distribuzione di Azure Stack.|`east`|
|Nome di dominio esterno|Il nome della zona a cui si desidera usare per la distribuzione di Azure Stack.|`cloud.fabrikam.com`|
|Nome di dominio interno|Il nome della zona interna che viene usato per i servizi di infrastruttura in Azure Stack.  È integrato al servizio Directory e privati (non raggiungibile dall'esterno della distribuzione di Azure Stack).|`azurestack.local`|
|Server d'inoltro DNS|Server DNS utilizzati per inoltrare query DNS, zone e record DNS ospitati all'esterno di Azure Stack, sia nella rete intranet aziendale o in internet pubblico.|`10.57.175.34`<br>`8.8.8.8`|
|Denominazione di prefisso (facoltativo)|Il prefisso di denominazione che dai nomi di computer Azure Stack dell'infrastruttura ruolo istanza disporre.  Se non specificato, il valore predefinito è `azs`.|`azs`|

Il nome di dominio completo (FQDN) di distribuzione di Azure Stack e gli endpoint è la combinazione del parametro di area e il parametro di nome di dominio esterno. Usando i valori degli esempi nella tabella precedente, il nome di dominio completo per la distribuzione di Azure Stack sarà il seguente nome:

`east.cloud.fabrikam.com`

Di conseguenza, gli esempi di alcuni degli endpoint per la distribuzione si presenta come gli URL seguenti:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Per usare questo spazio dei nomi DNS di esempio per la distribuzione di Azure Stack, sono necessarie le seguenti condizioni:

- La zona `fabrikam.com` è registrato con un registrar di dominio, un server DNS azienda interno o entrambi, a seconda dei requisiti di risoluzione del nome.
- Dominio figlio `cloud.fabrikam.com` esista nella zona `fabrikam.com`.
- I server DNS che ospitano le zone `fabrikam.com` e `cloud.fabrikam.com` sia raggiungibile dalla distribuzione di Azure Stack.

Per essere in grado di risolvere nomi DNS per gli endpoint di Azure Stack e le istanze dall'esterno di Azure Stack, è necessario integrare il server DNS che ospitano la zona DNS esterna per Azure Stack con i server DNS che ospitano la zona padre da usare.


## <a name="resolution-and-delegation"></a>Risoluzione e delega

Esistono due tipi di server DNS:

- Un server DNS autorevole ospita le zone DNS. e risponde alle query DNS solo per i record presenti in tali zone.
- Un server DNS ricorsivo non ospita le zone DNS. ma risponde a tutte le query DNS, chiamando i server DNS autorevoli per raccogliere tutti i dati necessari.

Azure Stack include entrambi autorevole e server DNS ricorsivi. Il server ricorsivi usati per risolvere i nomi di tutti gli elementi tranne la zona privata interna e la zona DNS pubblica esterna per la distribuzione di Azure Stack. 

![Architettura di Azure Stack DNS](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Risoluzione dei nomi DNS esterni da Azure Stack

Per risolvere i nomi DNS per gli endpoint all'esterno di Azure Stack (ad esempio: www.bing.com), è necessario fornire i server DNS che Azure Stack è possibile usare per inoltrare le richieste DNS per il quale non è autorevole Azure Stack. Per la distribuzione, sono necessari server DNS che Azure Stack inoltra le richieste nel foglio di lavoro di distribuzione (nel campo del server d'inoltro DNS). Specificare almeno due server in questo campo per la tolleranza di errore. Senza questi valori, si verifica un errore di distribuzione di Azure Stack.

### <a name="configure-conditional-dns-forwarding"></a>Configurare l'inoltro condizionale DNS

> [!IMPORTANT]
> Si applica solo a una distribuzione di AD FS.

Per abilitare la risoluzione dei nomi con l'infrastruttura DNS esistente, configurare l'inoltro condizionale.

Per aggiungere un server d'inoltro condizionale, è necessario usare l'endpoint con privilegi.

Per questa procedura, utilizzare un computer nella rete datacenter in grado di comunicare con l'endpoint con privilegi in Azure Stack.

1. Aprire una sessione di Windows PowerShell con privilegi elevata (Esegui come amministratore) e connettersi all'indirizzo IP dell'endpoint con privilegi. Usare le credenziali per l'autenticazione CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Dopo la connessione all'endpoint con privilegi, eseguire il comando PowerShell seguente. Sostituire i valori di esempio forniti con il nome di dominio e gli indirizzi IP dei server DNS da usare.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Risoluzione dei nomi DNS di Azure Stack dall'esterno di Azure Stack
I server autorevoli sono quelli che contengono le informazioni sulla zona DNS esterne e qualsiasi zona creata dall'utente. Integrazione con questi server per abilitare la delega di zona o l'inoltro condizionale per la risoluzione dei nomi DNS di Azure Stack dall'esterno di Azure Stack.

## <a name="get-dns-server-external-endpoint-information"></a>Ottenere informazioni sull'endpoint esterni di Server DNS

Per integrare la distribuzione di Azure Stack con l'infrastruttura DNS, sono necessarie le informazioni seguenti:

- Server DNS FQDN
- Indirizzi IP del server DNS

I nomi di dominio completi per i server DNS di Azure Stack hanno il formato seguente:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Usando i valori di esempio, il FQDN per il DNS Server sono:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Questa informazione viene anche creata alla fine di tutte le distribuzioni di Azure Stack in un file denominato `AzureStackStampDeploymentInfo.json`. Questo file si trova nel `C:\CloudDeployment\logs` cartella della macchina virtuale la distribuzione. Se non si conosce quali valori sono stati usati per la distribuzione di Azure Stack, è possibile ottenere i valori da qui.

Se la macchina virtuale di distribuzione non è più disponibile o è inaccessibile, è possibile ottenere i valori per la connessione all'endpoint con privilegi e l'esecuzione di `Get-AzureStackInfo` cmdlet di PowerShell. Per altre informazioni, vedere [privileged endpoint](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Configurazione di inoltro condizionale in Azure Stack

Il modo più semplice e sicuro per l'integrazione di Azure Stack con l'infrastruttura DNS consiste nell'eseguire l'inoltro condizionale della zona dal server che ospita la zona padre. Questo approccio è consigliato se si ha controllo diretto su server DNS che ospitano la zona padre per i nomi DNS esterno Azure Stack.

Se non si ha familiarità con le procedure per eseguire operazioni con il servizio DNS di inoltro condizionale, vedere l'articolo di TechNet seguente: [Assegnare un server d'inoltro condizionale per un nome di dominio](https://technet.microsoft.com/library/cc794735), o la documentazione specifica per la soluzione DNS.

Negli scenari in cui è specificata la zona di DNS di Stack di Azure esterno per l'aspetto di un dominio figlio del nome di dominio aziendale, l'inoltro condizionale non è utilizzabile. È necessario configurare la delega DNS.

Esempio:

- Nome di dominio DNS aziendale: `contoso.com`
- Nome di dominio DNS esterni di Azure Stack: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegare la zona DNS esterna ad Azure Stack

Per i nomi DNS essere risolto all'esterno di una distribuzione di Azure Stack, è necessario configurare la delega DNS.

Ogni registrar prevede i propri strumenti di gestione DNS per modificare i record del server dei nomi per un dominio. Nella pagina di gestione DNS del registrar, modificare i record NS e sostituirli i record NS per la zona con quelli in Azure Stack.

La maggior parte dei Registrar DNS richiedono di fornire un minimo di due server DNS per completare la delega.

## <a name="next-steps"></a>Passaggi successivi

[Integrazione con firewall](azure-stack-firewall.md)
