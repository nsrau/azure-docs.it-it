---
title: Azure requisiti dei certificati di infrastruttura a chiave pubblica dello Stack per Azure Stack integrati sistemi | Documenti Microsoft
description: Descrive i requisiti di distribuzione certificato PKI dello Stack di Azure per i sistemi Azure Stack integrato.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 9a43179998e8377dfbbb1a41ba7d46936d63aedd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030156"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Requisiti dei certificati di infrastruttura a chiave pubblica Azure Stack

Stack di Azure dispone di una rete pubblica infrastruttura utilizzando accessibile dall'esterno indirizzi IP pubblici assegnati a un piccolo set di servizi di Azure Stack ed eventualmente macchine virtuali tenant. I certificati PKI con i nomi DNS appropriati per questi endpoint infrastruttura pubblica dello Stack di Azure sono necessari durante la distribuzione di Azure Stack. In questo articolo contiene informazioni su:

- I certificati sono necessari per distribuire Azure Stack
- Il processo di richiesta di certificati corrispondenti tali specifiche
- Come preparare, convalidare e utilizzare tali certificati durante la distribuzione

> [!Note]  
> Durante la distribuzione è necessario copiare i certificati nella cartella di distribuzione che corrisponde al provider di identità che si sta distribuendo contro (Azure Active Directory o AD FS). Se si utilizza un solo certificato per tutti gli endpoint, è necessario copiare il file di certificato in ogni cartella di distribuzione come descritto nelle tabelle seguenti. La struttura di cartelle pre-compilata nella macchina virtuale distribuzione e possono essere trovata in: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Requisiti dei certificati
L'elenco seguente descrive i requisiti del certificato che sono necessari per distribuire Azure Stack: 
- I certificati devono essere emessi da un'autorità di certificazione interne o un'autorità di certificazione pubblica. Se si utilizza un'autorità di certificazione pubblica, deve essere incluso nell'immagine sistema operativo di base come parte del programma Microsoft Root autorità attendibili. È possibile trovare l'elenco completo di seguito: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- L'infrastruttura di Azure Stack deve avere l'accesso alla rete posizione elenco certificati revocati (CRL) dell'autorità di certificazione pubblicata nel certificato. Il CRL deve essere un endpoint http
- Quando la rotazione di certificati, certificati devono essere che uno rilasciato dall'autorità di certificazione interna stesso utilizzato per firmare i certificati nella distribuzione o qualsiasi autorità di certificazione pubblica sopra
- L'utilizzo di certificati autofirmati non sono supportati
- Il certificato può essere un certificato con caratteri jolly singolo che copre tutti spazi dei nomi nel campo nome alternativo soggetto (SAN). In alternativa, è possibile utilizzare certificati singoli utilizzando i caratteri jolly per gli endpoint, ad esempio **acs** e insieme di credenziali chiave in cui sono necessarie. 
- L'algoritmo di firma del certificato non può essere SHA1, deve essere maggiore. 
- Il formato del certificato deve essere PFX, come le chiavi pubbliche e private sono necessari per l'installazione dello Stack di Azure. 
- Il file pfx del certificato devono avere un valore "Firma digitale" e "KeyEncipherment" nel campo "Key Usage".
- Il file pfx del certificato devono avere i valori "Autenticazione Server (1.3.6.1.5.5.7.3.1)" e "Autenticazione Client (1.3.6.1.5.5.7.3.2)" nel campo "Enhanced Key Usage".
- Il certificato "rilasciato a:" campo non deve essere lo stesso come relativo "rilasciato da:" campo.
- Le password per tutti i file di certificato pfx devono essere uguale al momento della distribuzione
- Password per il file di certificato pfx deve essere una password complessa.
- Verificare che i nomi di soggetto e nomi alternativi del soggetto nella corrispondenza (x509v3_config) estensione nome alternativo soggetto. Il campo nome alternativo oggetto consente di specificare nomi host aggiuntivi (siti Web, gli indirizzi IP, nomi comuni) per essere protetti da un solo certificato SSL.

> [!NOTE]  
> Utente corrente non sono supportati i certificati firmati.

> [!NOTE]  
> La presenza di intermediario di autorità di certificazione in catena di trust è di un certificato supportata. 

## <a name="mandatory-certificates"></a>Certificati obbligatori
La tabella in questa sezione illustra i certificati di infrastruttura a chiave pubblica di endpoint pubblico dello Stack di Azure che sono necessari per entrambi Azure AD e le distribuzioni di AD FS Azure Stack. Requisiti dei certificati sono raggruppati per area, nonché gli spazi dei nomi e i certificati che sono necessari per ogni spazio dei nomi. La tabella descrive anche la cartella in cui il provider di soluzioni consente di copiare certificati diversi per ogni endpoint pubblici. 

I certificati con i nomi DNS appropriati per ogni endpoint di infrastruttura pubblica dello Stack di Azure sono necessari. Nome DNS dell'endpoint di ogni tipo viene espresso nel formato:  *&lt;prefisso >.&lt; area >. &lt;fqdn >*. 

Per la distribuzione, [region] e [externalfqdn] i valori devono corrispondere i nomi di dominio esterno che si è scelto per il sistema Azure Stack e area. Ad esempio, se il nome dell'area è stata *Redmond* e il nome di dominio esterno fosse *contoso.com*, i nomi DNS potrebbero avere il formato *&lt;prefisso >. redmond.contoso.com*. Il  *&lt;prefisso >* valori sono prestabiliti da Microsoft per descrivere l'endpoint protetto mediante il certificato. Inoltre, il  *&lt;prefisso >* i valori degli endpoint infrastruttura esterno dipendono dal servizio Azure Stack che utilizza l'endpoint specifico. 

> [!note]  
> Certificati possono essere fornite come un certificato con caratteri jolly singolo copertura di tutti gli spazi dei nomi nei campi del soggetto e nome alternativo soggetto (SAN) copiati in tutte le directory o certificati individuali per ogni endpoint copiato nella directory del corrispondente. Tenere presente che entrambe le opzioni è necessario utilizzare certificati con caratteri jolly per gli endpoint, ad esempio **acs** e insieme di credenziali chiave in cui sono necessarie. 

| Cartella di distribuzione | Soggetto certificato richiesto e nomi alternativi del soggetto (SAN) | Ambito (regione) | Spazio dei nomi di sottodominio |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Portale pubblico | portal.&lt;region>.&lt;fqdn> | Portali | &lt;region>.&lt;fqdn> |
| Portale di amministrazione | adminportal.&lt;region>.&lt;fqdn> | Portali | &lt;region>.&lt;fqdn> |
| Pubblico di gestione risorse di Azure | gestione. &lt;area >. &lt;fqdn > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Amministrazione di gestione risorse di Azure | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) | Archiviazione BLOB | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) | Archiviazione tabelle | tavolo. &lt;area >. &lt;fqdn > |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) | Archiviazione code | queue.&lt;region>.&lt;fqdn> |
| Insieme di credenziali delle chiavi | *.vault.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) |  Keyvault interno |  adminvault.&lt;region>.&lt;fqdn> |

Se si distribuisce Azure Stack usando la modalità di distribuzione di Azure AD, è sufficiente richiedere i certificati elencati nella tabella precedente. Tuttavia, se si distribuisce Azure Stack usando la modalità di distribuzione di ADFS, è necessario richiedere i certificati descritti nella tabella seguente:

|Cartella di distribuzione|Soggetto certificato richiesto e nomi alternativi del soggetto (SAN)|Ambito (regione)|Spazio dei nomi di sottodominio|
|-----|-----|-----|-----|
|AD FS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL)|AD FS|*&lt;region>.&lt;fqdn>*|
|Grafico|graph.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL)|Grafico|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Tutti i certificati elencati in questa sezione devono avere la stessa password. 

## <a name="optional-paas-certificates"></a>Certificati PaaS facoltativi
Se si intende distribuire i servizi aggiuntivi di Azure Stack PaaS (SQL, MySQL e servizio App) dopo aver dello Stack di Azure è stato distribuito e configurato, è necessario richiedere certificati aggiuntivi per coprire gli endpoint dei servizi PaaS. 

> [!IMPORTANT]
> I certificati utilizzati per i provider di risorse di servizio App, SQL e MySQL devono avere la stessa autorità radice come quelle utilizzate per gli endpoint di Azure Stack globali. 

La tabella seguente descrive gli endpoint e i certificati necessari per gli adapter SQL e MySQL e di servizio App. Non è necessario copiare questi certificati per la cartella di distribuzione dello Stack di Azure. Al contrario, questi certificati vengono forniti quando si installa il provider di risorse aggiuntive. 

|Ambito (regione)|Certificate|Soggetto certificato richiesto e nomi alternativo del soggetto (SAN)|Spazio dei nomi di sottodominio|
|-----|-----|-----|-----|
|SQL, MySQL|SQL e MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL con carattere jolly)|dbadapter.*&lt;region>.&lt;fqdn>*|
|Servizio app|Certificato SSL predefinito di traffico Web|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL con carattere jolly dominio multiserver<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> richiede un certificato con più nomi alternativi del soggetto con caratteri jolly. Carattere jolly più reti SAN in un singolo certificato potrebbe non essere supportato da tutte le autorità di certificazione pubblica 

<sup>2</sup> A &#42;.appservice. *&lt;area >. &lt;fqdn >* certificato con caratteri jolly non può essere utilizzato al posto di queste tre certificati (api.appservice. *&lt;area >. &lt;fqdn >*, ftp.appservice. *&lt;area >. &lt;fqdn >* e sso.appservice. *&lt;area >. &lt;fqdn >*. Servizio App richiede in modo esplicito l'utilizzo di certificati separati per questi endpoint. 

## <a name="learn-more"></a>Altre informazioni
Informazioni su come [generare i certificati PKI per la distribuzione di Azure Stack](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Passaggi successivi
[Integrazione di identità](azure-stack-integrate-identity.md)

