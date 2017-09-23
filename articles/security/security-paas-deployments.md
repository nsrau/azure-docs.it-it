---
title: Protezione delle distribuzioni PaaS | Microsoft Docs
description: " Comprendere i vantaggi di sicurezza del modello PaaS rispetto ad altri modelli di servizio cloud e conoscere le procedure consigliate per proteggere una distribuzione PaaS di Azure. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f218fe7e59e46683b544fd83bfea505b7cbe2d59
ms.contentlocale: it-it
ms.lasthandoff: 03/22/2017

---
# <a name="securing-paas-deployments"></a>Protezione delle distribuzioni PaaS

In questo articolo vengono fornite informazioni che consentono di:

- Comprendere i vantaggi di sicurezza dell'hosting delle applicazioni nel cloud
- Valutare i vantaggi di sicurezza del modello di piattaforma distribuita come servizio (PaaS, Platform as a Service) rispetto ad altri modelli di servizio cloud
- Modificare l'attenzione verso la sicurezza, da un approccio di sicurezza del perimetro incentrato sulla rete a uno incentrato sulle identità
- Implementare le procedure consigliate per la sicurezza del modello PaaS

## <a name="cloud-security-advantages"></a>Vantaggi della sicurezza cloud
Il cloud offre notevoli vantaggi legati alla sicurezza. In un ambiente locale, le organizzazioni si ritrovano probabilmente con responsabilità non ancora gestite e con risorse limitate disponibili da investire nella sicurezza. Tutto questo porta a un ambiente in cui i malintenzionati sono in grado di sfruttare vulnerabilità a più livelli.

![Vantaggi di sicurezza dell'era cloud][1]

Le organizzazioni possono migliorare la i tempi di rilevazione delle minacce e i tempi di risposta usando le funzionalità di sicurezza basate sul cloud e l'intelligence cloud di un provider.  Passando le responsabilità al provider di servizi cloud, le organizzazioni possono godere di una sicurezza più ampia e possono così riallocare le relative risorse e i relativi budget ad altre priorità aziendali.

## <a name="division-of-responsibility"></a>Suddivisione della responsabilità
È importante comprendere la suddivisione di responsabilità tra Microsoft e l'utente. In locale, si è proprietari dell'intero stack ma, con lo spostamento nel cloud, alcune responsabilità vengono trasferite a Microsoft. La matrice di responsabilità seguente mostra le aree dello stack in distribuzioni SaaS, PaaS e IaaS. Vengono indicate quelle di responsabilità dell'utente e quelle di responsabilità di Microsoft.

![Aree di responsabilità][2]

Per tutti i tipi di distribuzione cloud, l'utente è proprietario di dati e identità. È responsabilità dell'utente proteggere tali dati e identità, nonché le risorse locali e i componenti cloud da lui gestiti. Questi ultimi fattori possono variare in base al tipo di servizio.

L'utente ha comunque in ogni caso la responsabilità di quanto segue, a prescindere dal tipo di distribuzione:

- Dati
- Endpoint
- Account
- gestione degli accessi

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Vantaggi di sicurezza di un modello di servizio cloud PaaS
Verranno ora illustrati i vantaggi di sicurezza di una distribuzione PaaS di Azure rispetto a una locale, usando la stessa matrice di responsabilità.

![Vantaggi di sicurezza del modello PaaS][3]

Microsoft consente di ridurre i rischi e le responsabilità comuni già a partire dal fondo dello stack, ovvero l'infrastruttura fisica. Poiché viene costantemente monitorato da Microsoft stessa, il cloud di Microsoft è estremamente resistente agli attacchi. Pertanto non ha senso che venga considerato un bersaglio. A meno che non disponga di parecchio denaro e parecchie risorse, un malintenzionato sceglierà più probabilmente altri obiettivi.  

Al centro dello stack non c'è differenza tra una distribuzione PaaS e una locale. I rischi sono simili a livello di applicazione e a livello di gestione dell'account e degli accessi. Nella sezione dell'articolo dedicata ai passaggi successivi verranno illustrate le procedure consigliate per eliminare o ridurre al minimo tali rischi.

Nella parte superiore dello stack, governance dei dati e Rights Management, è possibile ridurre un rischio grazie alla gestione delle chiavi, descritta nelle procedure consigliate. La gestione delle chiavi è una responsabilità aggiuntiva, ma d'altro canto una distribuzione PaaS elimina la necessità di gestione di alcune aree, consentendo così di dedicare risorse proprio alla gestione delle chiavi.

La piattaforma di Azure offre inoltre una protezione avanzata dagli attacchi DDoS grazie a varie tecnologie basate su rete. Tuttavia, tutti i tipi di protezione da attacchi DDoS basati su rete hanno vari limiti per quanto riguarda collegamenti e data center. Per evitare le problematiche legate agli attacchi DDoS di portata elevata, è possibile sfruttare la funzionalità cloud centrale di Azure per usare rapidamente e automaticamente la scalabilità come difesa da questi attacchi. Negli articoli sulle procedure consigliate verranno illustrati in dettaglio i vari passaggi.

## <a name="modernizing-the-defenders-mindset"></a>Modernizzazione dell'approccio di difesa
Le distribuzioni PaaS impongono un cambiamento dell'approccio generale verso la sicurezza. Si passa da un controllo autonomo sostanzialmente totale alla condivisione di alcune responsabilità con Microsoft.

Un'altra differenza significativa tra le distribuzioni PaaS e quelle locali tradizionali consiste in una nuova visione di ciò che compone il perimetro di sicurezza primario. In passato, il perimetro di sicurezza locale primario era rappresentato dalla rete, con la maggior parte delle progettazioni di sicurezza locali che la usava come risorsa di sicurezza primaria. Per le distribuzioni PaaS, è consigliabile passare all'identità come perimetro di sicurezza primario.

## <a name="identity-as-the-primary-security-perimeter"></a>L'identità come perimetro di sicurezza primario
Una delle cinque caratteristiche essenziali del cloud computing consiste nell'ampio accesso alla rete, cosa che rende meno efficace un approccio incentrato su di essa. L'obiettivo della maggior parte del cloud computing è consentire l'accesso agli utenti a prescindere dalla loro posizione. La maggior parte di loro si troverà da qualche parte in Internet.

Nella figura seguente viene illustrata l'evoluzione del perimetro di sicurezza, da perimetro di rete a perimetro di identità. La sicurezza consiste sempre meno nel difendere la rete e sempre più nel difendere i dati e nel gestire la sicurezza di app e utenti. La differenza principale sta nel voler avvicinare il più possibile la sicurezza alle priorità dell'azienda.

![L'identità come nuovo perimetro di sicurezza][4]

Inizialmente, i servizi PaaS di Azure come i ruoli Web e SQL di Azure non offrivano difese con perimetri di rete tradizionali. Questo perché si presupponeva che l'esposizione a Internet fosse proprio lo scopo del ruolo Web e che l'autenticazione fosse essa stessa il nuovo perimetro, ad esempio BLOB o SQL di Azure.

Le procedure di sicurezza moderne presuppongono che un malintenzionato abbia violato il perimetro di rete. Pertanto, le procedure di difesa moderne si sono focalizzate sull'identità. Le organizzazioni devono stabilire un perimetro di sicurezza basato sull'identità con procedure consigliate di autenticazione e autorizzazione estremamente robuste.

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Suggerimenti per la gestione del perimetro di identità

I principi e i modelli per i perimetri di rete sono disponibili da molto tempo, tuttavia nel settore c'è meno esperienza riguardo l'uso dell'identità come perimetro di sicurezza primario. Ciò premesso, l'esperienza accumulata è comunque sufficiente per dare alcuni consigli generali la cui efficacia è comprovata sul campo, applicabili a quasi tutti i servizi PaaS.

Di seguito vengono riepilogate le procedure consigliate generali per la gestione del perimetro di identità.

- **Non perdere le chiavi o le credenziali** La protezione di chiavi e credenziali è essenziale per proteggere le distribuzioni PaaS. Perdere chiavi e credenziali è un problema comune. Una buona soluzione consiste nell'usare una soluzione centralizzata in cui le chiavi e i segreti possono essere archiviati in moduli di protezione hardware. Azure offre un modulo di protezione hardware nel cloud con [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Non inserire le credenziali e altri segreti nel codice sorgente o GitHub** Un rischio ben peggiore della perdita di chiavi e credenziali consiste negli accessi non autorizzati. Gli utenti malintenzionati sono in grado di sfruttare le tecnologie bot per trovare chiavi e segreti archiviati in repository di codice come GitHub. Si consiglia pertanto di non inserire chiavi e segreti in questi archivi di codice sorgente pubblici.
- **Proteggere le interfacce di gestione delle macchine virtuali nei servizi PaaS e IaaS ibridi** I servizi IaaS e PaaS vengono eseguiti in macchine virtuali. A seconda del tipo di servizio, sono disponibili diverse interfacce di gestione che consentono di gestire direttamente da remoto tali macchine virtuali. È possibile usare protocolli di gestione remota, ad esempio [Secure Shell Protocol (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), [Remote Desktop Protocol (RDP)](https://support.microsoft.com/kb/186607) e [Remote PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting). In generale, è consigliabile non abilitare l'accesso remoto diretto alle macchine virtuali da Internet. Se disponibile, è consigliabile usare approcci alternativi, ad esempio tramite una rete virtuale privata di Azure. Se non sono disponibili soluzioni alternative, assicurarsi di usare passphrase complesse e, se disponibile, l'autenticazione a due fattori (ad esempio [Multi-Factor Authentication di Azure](../multi-factor-authentication/multi-factor-authentication.md)).
- **Usare piattaforme di autenticazione e autorizzazione robuste**

  - Usare le identità federate in Azure AD invece degli archivi utente personalizzati. Quando si usano identità federate, è possibile sfruttare un approccio basato sulla piattaforma e delegare ai partner la gestione delle identità autorizzate. Un approccio con identità federate è particolarmente importante negli scenari in cui i dipendenti vengono rimossi e le modifiche devono essere applicate in più sistemi di identità e autorizzazioni.
  - Usare i meccanismi di autenticazione e autorizzazione forniti dalla piattaforma invece di un codice personalizzato poiché sviluppare un codice di autenticazione personalizzato può dare luogo a errori. La maggior parte degli sviluppatori non sarà esperta in sicurezza e probabilmente non conoscerà tutte le sfaccettature e gli ultimi sviluppi legati ad autenticazione e autorizzazione. Il codice commerciale, ad esempio quello di Microsoft, è spesso soggetto a rigorose analisi di sicurezza.
  - Usare l'autenticazione a più fattori. L'autenticazione a più fattori è è lo standard attuale per l'autenticazione e l'autorizzazione, in quanto permette di evitare le lacune di sicurezza intrinseche nei tipi di autenticazione basati su nome utente e password. L'accesso a entrambe le interfacce di gestione di Azure, ovvero il portale e PowerShell da remoto, e ai servizi per i clienti deve essere progettato e configurato per l'uso di [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md).
  - Usare protocolli di autenticazione standard come OAuth2 e Kerberos. Questi protocolli sono stati ampiamente analizzati e sono probabilmente implementati come parte delle librerie della piattaforma per autenticazione e autorizzazione.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati illustrati i vantaggi di sicurezza di una distribuzione PaaS di Azure. Il passaggio successivo è costituito dall'approfondimento delle procedure consigliate per proteggere le soluzioni PaaS Web e mobili. Si inizierà dai servizi app di Azure, dal database SQL di Azure e da SQL Data Warehouse di Azure. Non appena saranno disponibili le procedure consigliate per altri servizi Azure, nell'elenco seguente verranno inseriti i relativi collegamenti:

- [servizio app di Azure](security-paas-applications-using-app-services.md)
- [Database SQL di Azure e SQL Data Warehouse di Azure](security-paas-applications-using-sql.md)
- Archiviazione di Azure
- Cache REDIS di Azure
- Bus di servizio di Azure
- Web application firewall

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png

