---
title: I sistemi integrati di Azure requisiti dei certificati di infrastruttura a chiave pubblica dello Stack per Azure Stack | Microsoft Docs
description: Descrive i requisiti di distribuzione di certificati di infrastruttura a chiave pubblica di Azure Stack per i sistemi integrati di Azure Stack.
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
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 8de34e4ac01dea9cf4a0c718883e8cc828be6403
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714618"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Requisiti dei certificati di infrastruttura a chiave pubblica Azure Stack

Azure Stack ha una rete pubblica infrastruttura usando accessibili esternamente indirizzi IP pubblici assegnati a un piccolo set di servizi di Azure Stack ed eventualmente le macchine virtuali tenant. I certificati PKI con i nomi DNS appropriati per questi endpoint infrastruttura pubblica di Azure Stack sono necessari durante la distribuzione di Azure Stack. Questo articolo contiene informazioni su:

- Quali certificati sono necessari per distribuire Azure Stack
- Il processo per ottenere i certificati corrispondenti a quelle specifiche
- Come preparare, convalidare e usare tali certificati durante la distribuzione

> [!Note]  
> Durante la distribuzione è necessario copiare i certificati per la cartella di distribuzione che corrisponde al provider di identità che si sta distribuendo contro (Azure AD o AD FS). Se si usa un solo certificato per tutti gli endpoint, è necessario copiare tale file di certificato in ogni cartella di distribuzione come descritto nelle tabelle seguenti. La struttura di cartelle è pre-compilata nella macchina virtuale distribuzione e sono reperibili in: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Requisiti dei certificati
L'elenco seguente descrive i requisiti dei certificati necessari per distribuire Azure Stack: 
- I certificati devono essere emesso da un'autorità di certificazione interna o da un'autorità di certificazione pubblica. Se si usa un'autorità di certificazione pubblica, deve essere incluso nell'immagine sistema operativo di base durante il programma Microsoft Root autorità attendibili. È possibile trovare l'elenco completo di seguito: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- L'infrastruttura di Azure Stack deve avere accesso di rete in percorso di elenco di revoche di certificati (CRL) dell'autorità di certificazione pubblicato nel certificato. Il CRL deve essere un endpoint http
- Ruotare i certificati, i certificati devono essere che entrambi emesso dall'autorità di certificazione interna stesso utilizzato per firmare i certificati specificati alla distribuzione o qualsiasi autorità di certificazione pubblica in precedenza
- L'uso di certificati autofirmati non sono supportati
- Per la distribuzione e la rotazione è possibile utilizzare un solo certificato che coprono tutti spazi dei nomi nei campi nome soggetto e nome alternativo soggetto (SAN) del certificato oppure è possibile usare singoli certificati per ogni spazio dei nomi seguente che Azure Stack si prevede di utilizzare i servizi richiedono. Nota: entrambi gli approcci richiedono l'uso di caratteri jolly per gli endpoint in cui sono necessarie, ad esempio **KeyVault** e **KeyVaultInternal**. 
- L'algoritmo di firma del certificato deve essere 3DES. L'algoritmo non può essere SHA1, che deve essere più avanzato. 
- Il formato del certificato deve essere PFX, perché entrambe le chiavi pubbliche e private sono necessarie per l'installazione di Azure Stack. 
- Il file pfx del certificato devono avere un valore "Firma digitale" e "KeyEncipherment" nel campo relativo al "Key Usage".
- Il file pfx del certificato devono avere i valori "Autenticazione Server (1.3.6.1.5.5.7.3.1)" e "Autenticazione Client (1.3.6.1.5.5.7.3.2)" nel campo "Enhanced Key Usage".
- Il certificato "rilasciato a:" campo non deve essere lo stesso come relativo "rilasciato da:" campo.
- Le password per tutti i file di certificato pfx devono essere uguale al momento della distribuzione
- Password per il file di certificato pfx deve essere una password complessa.
- Verificare che i nomi di soggetto e nomi alternativi del soggetto nella corrispondenza (x509v3_config) estensione nome alternativo soggetto. Il campo nome alternativo soggetto consente di specificare i nomi host aggiuntivi (siti Web, gli indirizzi IP, nomi comuni) per essere protetti da un solo certificato SSL.

> [!NOTE]  
> Self-Signed certificati non sono supportati.

> [!NOTE]  
> È supportata la presenza di intermediario di autorità di certificazione in IS catena di relazioni di trust del certificato. 

## <a name="mandatory-certificates"></a>Certificati obbligatori
La tabella in questa sezione illustra i certificati di infrastruttura a chiave pubblica endpoint pubblico di Azure Stack che sono necessari per sia per Azure AD e le distribuzioni di AD FS Azure Stack. Requisiti del certificato sono raggruppati per area, nonché gli spazi dei nomi e i certificati che sono necessari per ogni spazio dei nomi. La tabella descrive anche la cartella in cui il provider di soluzioni consente di copiare i certificati diversi per ogni endpoint pubblico. 

I certificati con i nomi DNS appropriati per ogni endpoint infrastruttura pubblica di Azure Stack sono necessari. Nome DNS dell'endpoint di ogni tipo viene espresso nel formato:  *&lt;prefisso >.&lt; area >. &lt;fqdn >*. 

Per la distribuzione, [region] e [externalfqdn] i valori devono corrispondere all'area e i nomi di dominio esterno che si è scelto per il sistema Azure Stack. Ad esempio, se il nome dell'area è stata *Redmond* e il nome di dominio esterno era *contoso.com*, i nomi DNS potrebbero avere il formato *&lt;prefisso >. redmond.contoso.com*. Il  *&lt;prefisso >* i valori sono prestabiliti da Microsoft per descrivere l'endpoint protetto mediante il certificato. Inoltre, il  *&lt;prefisso >* i valori degli endpoint esterni infrastruttura dipendono dal servizio Azure Stack che usa l'endpoint specifico. 

> [!note]  
> Certificati possono essere forniti come un certificato con caratteri jolly singolo che coprono tutti gli spazi dei nomi nei campi del soggetto e nome alternativo soggetto (SAN) copiati in tutte le directory o certificati individuali per ogni endpoint copiato nella directory corrispondente. Tenere presente che entrambe le opzioni è necessario usare certificati con caratteri jolly per gli endpoint, ad esempio **acs** e Key Vault in cui sono necessarie. 

| Cartella di distribuzione | Soggetto certificato necessarie e nomi alternativi del soggetto (SAN) | Ambito (per area) | Spazio dei nomi di sottodominio |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Portale pubblico | portal.&lt;region>.&lt;fqdn> | Portali | &lt;region>.&lt;fqdn> |
| Portale di amministrazione | adminportal.&lt;region>.&lt;fqdn> | Portali | &lt;region>.&lt;fqdn> |
| Pubblico di Azure Resource Manager | gestione. &lt;area >. &lt;fqdn > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Amministrazione di Azure Resource Manager | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) | Archiviazione BLOB | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) | Archiviazione tabelle | tavolo. &lt;area >. &lt;fqdn > |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) | Archiviazione code | queue.&lt;region>.&lt;fqdn> |
| Insieme di credenziali delle chiavi | *.vault.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Certificato SSL con carattere jolly) |  Keyvault interno |  adminvault.&lt;region>.&lt;fqdn> |
| Host dell'estensione di amministrazione | *.adminhosting. \<area >. \<fqdn > (certificati SSL con caratteri jolly) | Host dell'estensione di amministrazione | adminhosting. \<area >. \<fqdn > |
| Host dell'estensione pubblica | * .hosting. \<area >. \<fqdn > (certificati SSL con caratteri jolly) | Host dell'estensione pubblica | Hosting. \<area >. \<fqdn > |

Se si distribuisce Azure Stack tramite la modalità di distribuzione di Azure AD, è sufficiente richiedere i certificati elencati nella tabella precedente. Tuttavia, se si distribuisce Azure Stack tramite la modalità di distribuzione di AD FS, è necessario richiedere certificati descritti nella tabella seguente:

|Cartella di distribuzione|Soggetto certificato necessarie e nomi alternativi del soggetto (SAN)|Ambito (per area)|Spazio dei nomi di sottodominio|
|-----|-----|-----|-----|
|AD FS|adfs.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL)|AD FS|*&lt;region>.&lt;fqdn>*|
|Grafico|graph.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL)|Grafico|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Tutti i certificati elencati in questa sezione devono avere la stessa password. 

## <a name="optional-paas-certificates"></a>Certificati di PaaS facoltativi
Se si prevede di distribuire i servizi PaaS di Azure Stack aggiuntivi (SQL, MySQL e servizio App) dopo Azure Stack è stato distribuito e configurato, è necessario richiedere certificati aggiuntivi per coprire gli endpoint dei servizi PaaS. 

> [!IMPORTANT]
> I certificati utilizzati per i provider risorse servizio App, SQL e MySQL devono avere la stessa autorità radice come quelli usati per gli endpoint di Azure Stack globali. 

La tabella seguente descrive gli endpoint e i certificati necessari per gli adapter SQL e MySQL e per il servizio App. Non è necessario copiare questi certificati per la cartella di distribuzione di Azure Stack. Al contrario, questi certificati vengono forniti quando si installa il provider di risorse aggiuntive. 

|Ambito (per area)|Certificate|Soggetto certificato necessarie e nomi alternativi soggetto (SAN)|Spazio dei nomi di sottodominio|
|-----|-----|-----|-----|
|SQL, MySQL|SQL e MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL con carattere jolly)|dbadapter.*&lt;region>.&lt;fqdn>*|
|Servizio app|Certificato SSL predefinito di traffico Web|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL con carattere jolly del dominio Multi<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Servizio app|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificato SSL<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> richiede un certificato con più nomi alternativi del soggetto con carattere jolly. Più caratteri jolly SAN in un singolo certificato potrebbe non essere supportato da tutte le autorità di certificazione pubbliche 

<sup>2</sup> A &#42;.appservice. *&lt;area >. &lt;fqdn >* certificato con caratteri jolly non può essere usato al posto di queste tre certificati (api.appservice. *&lt;area >. &lt;fqdn >*, ftp.appservice. *&lt;area >. &lt;fqdn >* e sso.appservice. *&lt;area >. &lt;fqdn >*. Servizio App richiede in modo esplicito l'uso di certificati separati per questi endpoint. 

## <a name="learn-more"></a>Altre informazioni
Informazioni su come [generare i certificati PKI per la distribuzione di Azure Stack](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Passaggi successivi
[Integrazione delle identità](azure-stack-integrate-identity.md)

