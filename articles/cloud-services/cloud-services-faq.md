---
title: Domande frequenti sui ruoli di Servizi cloud Azure | Documentazione Microsoft
description: Domande frequenti su Servizi cloud di Azure. L&quot;articolo contiene risposte ad alcune domande comuni su certificati, ruoli Web e ruoli di lavoro.
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 8dc7ea843ea316fa4659a8e6575adbfd045f7a70
ms.openlocfilehash: c169f9ab2eead732ad0fe5579caaa1b4b015732b


---
# <a name="cloud-services-faq"></a>Domande frequenti sui servizi cloud
Questo articolo risponde ad alcune domande frequenti sui servizi cloud di Microsoft Azure. Per informazioni generali sui prezzi e sul supporto di Azure, vedere [Domande frequenti sul supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083) . Per informazioni sulle dimensioni, vedere la pagina [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md) .

## <a name="certificates"></a>Certificati
### <a name="where-should-i-install-my-certificate"></a>Dove deve essere installato il certificato?
* **My**  
  Certificato dell'applicazione con chiave privata (con estensione \*pfx, \*p12).
* **CA**  
  Tutti i certificati intermedi, come CA secondari e criteri, vanno in questo archivio.
* **ROOT**  
  Archivio CA radice in cui inserire il certificato CA radice principale.

### <a name="i-cant-remove-expired-certificate"></a>Non è possibile rimuovere un certificato scaduto
Azure impedisce la rimozione di un certificato mentre viene usato. È necessario eliminare la distribuzione che fa uso del certificato o aggiornarla con un certificato diverso o rinnovato.

### <a name="delete-an-expired-certificate"></a>Eliminare un certificato scaduto
Se il certificato non viene usato, è possibile rimuoverlo mediante il cmdlet [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) di PowerShell.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Sono presenti certificati scaduti denominati Gestione dei servizi Microsoft Azure per le estensioni
Questi certificati vengono creati ogni volta che si aggiunge un'estensione al servizio cloud, ad esempio l'estensione Desktop remoto, e vengono usati unicamente per crittografare e decrittografare la configurazione privata dell'estensione. Non è un problema se questi certificati scadono, perché la data di scadenza non viene controllata.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>I certificati eliminati continuano a riapparire
Molto probabilmente questi certificati continuano a ricomparire a causa di uno strumento che si sta usando, ad esempio Visual Studio. Ogni volta che ci si riconnette con uno strumento che usa un certificato, questo viene caricato nuovamente in Azure.

### <a name="my-certificates-keep-disappearing"></a>I certificati continuano a scomparire
Durante il riciclo dell'istanza di macchina virtuale vengono perse tutte le modifiche locali. Usare un' [attività di avvio](cloud-services-startup-tasks.md) per installare i certificati nella macchina virtuale ogni volta che viene avviato il ruolo.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Non è possibile trovare i certificati di gestione nel portale
I [certificati di gestione](../azure-api-management-certs.md) sono disponibili solo nel portale di Azure classico. Il portale di Azure corrente non usa i certificati di gestione. 

### <a name="how-can-i-disable-a-management-certificate"></a>Come è possibile disabilitare un certificato di gestione?
[certificati di gestione](../azure-api-management-certs.md) non possono essere disabilitati. Vengono eliminati tramite il portale di Azure classico quando non si intende usarli più.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Come è possibile creare un certificato SSL per un indirizzo IP specifico?
Seguire le istruzioni nell' [esercitazione sulla creazione di un certificato](cloud-services-certs-create.md). Usare l'indirizzo IP come nome DNS.

## <a name="security"></a>Sicurezza
### <a name="disable-ssl-30"></a>Disabilitare SSL 3.0
Per disabilitare SSL 3.0 e usare la sicurezza TLS, creare un'attività di avvio documentata in questo post del blog: https://azure.microsoft.com/it-it/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Scalabilità di un servizio cloud
### <a name="i-cannot-scale-beyond-x-instances"></a>Impossibile eseguire la scalabilità per un numero di istanze superiore a X
La sottoscrizione di Azure presenta un limite al numero di memorie centrali che è possibile usare. Se vengono usate tutte le memorie centrali disponibili, la scalabilità non funziona. Ad esempio, se si dispone di un limite di 100 memorie centrali, vuol dire che è possibile avere 100 istanze di macchina virtuale con dimensioni A1 per il servizio cloud o 50 istanze di macchine virtuali con dimensioni A2.

## <a name="troubleshooting"></a>Risoluzione dei problemi
### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Non è possibile riservare un indirizzo IP in un servizio cloud con più indirizzi VIP
Assicurarsi prima di tutto che l'istanza di macchina virtuale per cui si sta provando a riservare l'indirizzo IP sia accesa. Assicurarsi quindi che vengano usati indirizzi IP riservati sia per la distribuzione di staging che per quella di produzione. **Non** modificare le impostazioni durante l'aggiornamento della distribuzione.




<!--HONumber=Jan17_HO4-->


