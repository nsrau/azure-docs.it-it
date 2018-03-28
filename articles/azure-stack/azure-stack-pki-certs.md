---
title: Azure requisiti dei certificati di infrastruttura a chiave pubblica dello Stack per Azure Stack integrati sistemi | Documenti Microsoft
description: Descrive i requisiti di distribuzione del certificato PKI dello Stack di Azure per i sistemi Azure Stack integrato.
services: azure-stack
documentationcenter: ''
author: mabriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 455c74ca808f71258a12166c2e36bdd73d9a3e20
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Requisiti dei certificati di infrastruttura a chiave pubblica Stack Azure
Stack Azure dispone di una rete pubblica infrastruttura utilizzano accessibile dall'esterno indirizzi IP pubblici assegnati a un piccolo set di servizi di Azure Stack ed eventualmente macchine virtuali tenant. I certificati PKI con i nomi DNS appropriati per questi endpoint infrastruttura pubblica Azure Stack sono necessari durante la distribuzione di Azure Stack. In questo articolo contiene informazioni su:

- I certificati sono necessari per distribuire Azure Stack
- Il processo di richiesta di certificati corrispondenti a tali specifiche
- Come preparare, convalidare e utilizzare tali certificati durante la distribuzione

> [!NOTE]
> Durante la distribuzione è necessario copiare i certificati per la cartella di distribuzione che corrisponde al provider di identità che si sta distribuendo contro (Azure Active Directory o AD FS). Se si utilizza un solo certificato per tutti gli endpoint, è necessario copiare il file di certificato in ogni cartella di distribuzione come descritto nelle tabelle seguenti. La struttura di cartelle è incorporata nella distribuzione macchina virtuale ed è disponibile in: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Requisiti dei certificati
L'elenco seguente descrive i requisiti del certificato che sono necessari per distribuire Azure Stack: 
- I certificati devono essere emessi da un'autorità di certificazione interna o un'autorità di certificazione pubblica. Se si utilizza un'autorità di certificazione pubblica, deve essere incluso nell'immagine sistema operativo di base durante il programma Microsoft Root autorità attendibili. È possibile trovare l'elenco completo di seguito: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Il certificato può essere un certificato con caratteri jolly singolo che coprono tutti spazi dei nomi nel campo nome alternativo soggetto (SAN). In alternativa, è possibile utilizzare i singoli certificati con caratteri jolly per gli endpoint, ad esempio acs e l'insieme di credenziali chiave in cui sono necessarie. 
- L'algoritmo di firma del certificato non può essere SHA1, deve essere maggiore. 
- Il formato del certificato deve essere PFX, come le chiavi pubbliche e private sono necessari per l'installazione dello Stack di Azure. 
- Il file pfx del certificato devono avere un valore "Firma digitale" e "KeyEncipherment" nel campo "Key Usage".
- Il file pfx del certificato devono avere i valori "Autenticazione Server (1.3.6.1.5.5.7.3.1)" e "Autenticazione Client (1.3.6.1.5.5.7.3.2)" nel campo "Utilizzo chiavi avanzato".
- Il certificato "rilasciato a:" campo non deve essere lo stesso come relativo "rilasciato da:" campo.
- Le password per tutti i file di certificato pfx devono essere uguale al momento della distribuzione
- Assicurarsi che i nomi di soggetto e i nomi di soggetto alternativo di tutti i certificati corrispondano a quanto descritto in questo articolo per evitare distribuzioni non riuscite.

> [!NOTE]
> La presenza di intermediario di autorità di certificazione in catena di relazioni di trust è di un certificato supportata. 

## <a name="mandatory-certificates"></a>Certificati obbligatori
Nella tabella in questa sezione vengono descritti i certificati PKI endpoint pubblico dello Stack di Azure sono richiesti per entrambi Azure AD e le distribuzioni di AD FS Azure Stack. Requisiti dei certificati vengono raggruppati per area, nonché gli spazi dei nomi e i certificati che sono necessari per ogni spazio dei nomi. La tabella descrive anche la cartella in cui il provider di soluzioni consente di copiare i certificati diversi per ogni endpoint pubblici. 

I certificati con i nomi DNS appropriati per ogni endpoint infrastruttura pubblica Azure Stack sono necessari. Nome DNS dell'endpoint di ogni tipo viene espresso nel formato:  *&lt;prefisso >.&lt; area >. &lt;fqdn >*. 

Per la distribuzione, [region] e [externalfqdn] i valori devono corrispondere i nomi di dominio esterno scelto per il sistema Azure Stack e l'area. Ad esempio, se il nome dell'area stato *Redmond* e il nome di dominio esterno è *contoso.com*, i nomi DNS potrebbero avere il formato *&lt;prefisso >. redmond.contoso.com*. Il  *&lt;prefisso >* valori sono prestabiliti da Microsoft per descrivere l'endpoint protetto mediante il certificato. Inoltre, il  *&lt;prefisso >* i valori degli endpoint infrastruttura esterno dipendono dal servizio Azure Stack che utilizza l'endpoint specifico. 

|Cartella di distribuzione|Soggetto certificato richiesto e nomi alternativi del soggetto (SAN)|Ambito (per regione)|Spazio dei nomi di sottodominio|
|-----|-----|-----|-----|
|Portale pubblico|portal.*&lt;region>.&lt;fqdn>*|Portali|*&lt;region>.&lt;fqdn>*|
|Portale di amministrazione|adminportal.*&lt;region>.&lt;fqdn>*|Portali|*&lt;region>.&lt;fqdn>*|
|Pubblico di gestione risorse di Azure|management.*&lt;region>.&lt;fqdn>*|Gestione risorse di Azure|*&lt;region>.&lt;fqdn>*|
|Amministrazione di gestione risorse di Azure|adminmanagement.*&lt;region>.&lt;fqdn>*|Gestione risorse di Azure|*&lt;region>.&lt;fqdn>*|
|ACS<sup>1</sup>|Un certificato con caratteri jolly multi-sottodominio con nomi alternativi soggetto per:<br>&#42;.blob.*&lt;region>.&lt;fqdn>*<br>&#42;.queue.*&lt;region>.&lt;fqdn>*<br>&#42;.table.*&lt;region>.&lt;fqdn>*|Archiviazione|blob.*&lt;region>.&lt;fqdn>*<br>table.*&lt;region>.&lt;fqdn>*<br>queue.*&lt;region>.&lt;fqdn>*|
|Insieme di credenziali delle chiavi|&#42;.vault.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL con carattere jolly)|Insieme di credenziali di chiave|vault.*&lt;region>.&lt;fqdn>*|
|KeyVaultInternal|&#42;.adminvault.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL con carattere jolly)|Keyvault interno|adminvault.*&lt;region>.&lt;fqdn>*|
|
<sup>1</sup> certificato del servizio ACS richiede tre SAN con caratteri jolly in un solo certificato. Carattere jolly più reti SAN in un singolo certificato potrebbe non essere supportata da tutte le autorità di certificazione pubblica. 

Se si distribuisce Azure Stack usando la modalità di distribuzione di Azure AD, è sufficiente richiedere i certificati elencati nella tabella precedente. Tuttavia, se si distribuisce Azure Stack usando la modalità di distribuzione di ADFS, è necessario richiedere i certificati descritti nella tabella seguente:

|Cartella di distribuzione|Soggetto certificato richiesto e nomi alternativi del soggetto (SAN)|Ambito (per regione)|Spazio dei nomi di sottodominio|
|-----|-----|-----|-----|
|AD FS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL)|AD FS|*&lt;region>.&lt;fqdn>*|
|Grafico|graph.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL)|Grafico|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Tutti i certificati elencati in questa sezione devono avere la stessa password. 

## <a name="optional-paas-certificates"></a>Certificati PaaS facoltativi
Se si prevede di distribuire i servizi aggiuntivi di Azure Stack PaaS (SQL, MySQL e servizio App) dopo dello Stack di Azure è stato distribuito e configurato, è necessario richiedere certificati aggiuntivi per coprire gli endpoint dei servizi PaaS. 

> [!IMPORTANT]
> I certificati utilizzati per i provider di risorse di servizio App, SQL e MySQL è necessario avere la stessa autorità radice come quelle utilizzate per gli endpoint di Azure Stack globali. 

La tabella seguente descrive gli endpoint e i certificati necessari per gli adapter SQL e MySQL e di servizio App. Non è necessario copiare i certificati per la cartella di distribuzione Azure Stack. Al contrario, vengono forniti questi certificati quando si installa il provider di risorse aggiuntive. 

|Ambito (per regione)|Certificate|Soggetto certificato richiesto e i nomi di soggetto alternativo (SAN)|Spazio dei nomi di sottodominio|
|-----|-----|-----|-----|
|SQL, MySQL|SQL e MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL con carattere jolly)|dbadapter.*&lt;region>.&lt;fqdn>*|
|Servizio app|Certificato SSL predefinito di traffico Web|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL con carattere jolly multidominio<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> richiede un certificato con più nomi alternativi del soggetto con caratteri jolly. Carattere jolly più reti SAN in un singolo certificato potrebbe non essere supportato da tutte le autorità di certificazione pubblica 

<sup>2</sup> A &#42;.appservice. *&lt;area >. &lt;fqdn >* certificato con caratteri jolly non può essere utilizzato al posto di queste tre certificati (api.appservice. *&lt;area >. &lt;fqdn >*, ftp.appservice. *&lt;area >. &lt;fqdn >*e sso.appservice. *&lt;area >. &lt;fqdn >*. Servizio App richiede in modo esplicito l'utilizzo di certificati separati per questi endpoint. 

## <a name="learn-more"></a>Altre informazioni
Informazioni su come [generare i certificati PKI per la distribuzione di Azure Stack](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Passaggi successivi
[Integrazione di identità](azure-stack-integrate-identity.md)

