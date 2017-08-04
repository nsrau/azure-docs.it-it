---
title: Modificare la fatturazione per Azure RemoteApp | Documentazione Microsoft
description: Informazioni su come interrompere la fatturazione per Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 8f94da9a-7848-4ddc-b7b7-d9c280ccf4d7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 32fc673eeef01e80c73375bf264206beea8cfbe5
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-from-azure-remoteapp-to-mycloudit"></a>Migrazione da Azure RemoteApp a MyCloudIT 

**Attualmente si usa Microsoft Azure RemoteApp?**  MyCloudIT ha creato uno strumento automatizzato per eseguire la migrazione delle raccolte Azure RemoteApp (ARA) alla piattaforma di gestione MyCloudIT, continuando a usare Microsoft Azure.

**Sfruttare il portale Azure Resource Manager**: la migrazione alla piattaforma MyCloudIT consente l'accesso immediato al nuovo portale Azure Resource Manager di Azure. Questo portale contiene tutte le nuove funzionalità e le innovazioni offerte da Microsoft Azure, incluso l'accesso alle dimensioni delle macchine virtuali per garantire che la distribuzione sia progettata per supportare le esigenze aziendali.

**Eseguire test in parallelo per garantire la soluzione più adatta per le specifiche esigenze**: lo strumento di migrazione MyCloudIT è progettato per avviare il processo di migrazione ed eseguire test in parallelo mentre gli utenti ARA correnti continuano a usare ARA.  Gli utenti rimarranno in ARA fino al completamento della migrazione e dei test.  Lo strumento di migrazione è progettato per gestire una tipica raccolta ARA.  In caso di particolari scenari non standard, scrivere all'indirizzo [ sales@conexlink.com ](mailto:sales@conexlink.com). Verrà proposto un piano personalizzato per agevolare la migrazione.

**Funzionalità Desktop-as-a-Service**: MyCloudIT offre non solo le funzionalità RemoteApp che gli utenti sono già abituati a usare, ma anche complete funzionalità Desktop-as-a-Service allo stesso costo mensile, senza alcun requisito minimo di utenti.

## <a name="what-we-will-do-for-you"></a>Quali operazioni vengono eseguite

MyCloudIT automatizza la migrazione del modello di Azure RemoteApp dal portale di Azure classico al portale di Azure Resource Manager della sottoscrizione del cliente attraverso uno strumento di migrazione automatica.  

> [!NOTE]
> Il modello di Azure RemoteApp deve rimanere nella stessa area di Azure della distribuzione di Azure RemoteApp originale.  Se è necessario modificare aree di Azure o sottoscrizioni di Azure durante la migrazione, scrivere all'indirizzo [ sales@conexlink.com ](mailto:sales@conexlink.com) per indicazioni aggiuntive.

Informazioni dettagliate sul processo di migrazione automatica con lo strumento di migrazione MyCloudIT sono riportate di seguito:

1. Lo strumento di migrazione analizza le sottoscrizioni correnti per tutte le distribuzioni ARA esistenti.  
2. Selezionare una raccolta ARA di cui eseguire la migrazione alla volta.  Se si dispone di più raccolte, eseguire lo strumento più volte.
3. È possibile scegliere di copiare i dischi dei profili utente (UPD) nella nuova distribuzione in modo da recuperare i dati legacy o associare manualmente gli UPD alla nuova distribuzione. Se si sceglie di copiare gli UPD, questi verranno salvati e sarà incluso un file di testo che associa il nome dell'UPD al nome di ciascun utente.  Gli UPD verranno copiati in una condivisione sul server RDSMGMT `F:\Shares\LegacyUPD` e saranno esposti tramite la condivisione `\\RDSmgmt\LegacyUPD`. 
4. La migrazione non comporterà tempi di inattività per la distribuzione ARA corrente.  Se tuttavia vengono apportate modifiche agli UPD (da ARA) dopo la copia, queste modifiche non saranno riflesse negli UPD archiviati nel portale di Azure Resource Manager. 
5. Se sono presenti altre macchine virtuali come controller di dominio e file server nella rete virtuale di Azure classica, verrà stabilito il peering tra la rete virtuale di Azure classica esistente e la nuova rete virtuale creata automaticamente nel nuovo portale di Azure Resource Manager.
6. La soluzione automatizzata stabilisce il peering tra la rete virtuale di Azure classica esistente e la nuova rete virtuale solo se la distribuzione ARA esistente è una distribuzione ibrida (ovvero, se si esegue l'autenticazione con un controller di dominio Windows Server Active Directory nella rete virtuale classica esistente). Se il peering delle reti virtuali è necessario per una raccolta ma non viene stabilito, scrivere all'indirizzo [sales@conexlink.com](mailto:sales@conexlink.com). Provvederemo a configurare il peering delle reti virtuali senza alcun costo aggiuntivo.
7. La soluzione automatizzata garantisce che la configurazione DNS di Azure venga aggiornata con le impostazioni della nuova rete virtuale, per assicurare che la nuova distribuzione possa connettersi al controller di dominio esistente nella rete virtuale classica.
8. La soluzione automatizzata garantisce che non siano presenti conflitti di indirizzi IP durante la creazione della nuova rete virtuale e del peering delle reti virtuali per le distribuzioni con un server Windows Server Active Directory esistente.
9. Se si usa solo Azure AD per l'autenticazione, MyCloudIT creerà un nuovo dominio Windows Server Active Directory e userà Azure AD Connect per sincronizzare gli utenti tra l'istanza esistente di Azure AD e il nuovo dominio Windows Server Active Directory creato da MyCloudIT.
10. Se si usa un dominio Windows Server Active Directory per l'autenticazione degli utenti ARA, la soluzione automatizzata connetterà la nuova distribuzione MyCloudIT al controller di dominio Windows Server Active Directory esistente tramite il peering delle reti virtuali.
11. Se si usa Azure Active Directory Domain Services per l'autenticazione, è possibile eseguire la migrazione ma è necessario contattarci per consentire la creazione di un piano di migrazione personalizzato.  Inviare un messaggio di posta elettronica all'indirizzo [sales@conexlink.com](mailto:sales@conexlink.com). 
12. Dopo avere confermato la migrazione della raccolta, è possibile attendere che la soluzione automatizzata esegua la migrazione della raccolta ARA e dei dischi dei profili utente (facoltativo) alla nuova soluzione RemoteApp basata su MyCloudIT.
13. Al termine della distribuzione, verranno pubblicate nuovamente le stesse applicazioni che erano pubblicate in ARA. Dopo la distribuzione, sarà possibile pubblicare applicazioni aggiuntive.

## <a name="post-migration-benefits"></a>Vantaggi dopo la migrazione

1. È disponibile una console di gestione che consente di gestire l'intero ciclo di vita della distribuzione RemoteApp.
2. Sarà possibile gestire le macchine virtuali dal portale.  Avviare, arrestare e ridimensionare le singole macchine virtuali, se necessario.
3. La console di gestione offre la possibilità di creare e gestire utenti o gruppi dal portale di gestione.
4. La console di gestione offre la possibilità di sincronizzare gli utenti con Office 365 per creare un'esperienza Same Sign-On.
5. La console di gestione offre la possibilità di creare altre raccolte RemoteApp e Desktop senza costi utente duplicati o con requisiti minimi per gli utenti. 
6. La console di gestione offre la possibilità di pubblicare nuove applicazioni RemoteApp.
7. La console di gestione offre la possibilità di pianificare l'avvio e l'arresto della distribuzione RemoteApp se la soluzione è necessaria solo in orari specifici.
8. La console di gestione offre la possibilità di automatizzare l'installazione e la configurazione dell'agente di Backup di Azure, che fornisce una cronologia di conservazione dei documenti per i dati sui clienti.
9. La console di gestione fornisce l'accesso alle metriche delle prestazioni della distribuzione.  Questo consente di identificare i potenziali colli di bottiglia delle prestazioni senza installare strumenti di gestione aggiuntivi per le prestazioni.
10. Se sono presenti più host sessione, è possibile abilitare la scalabilità automatica, in modo che vengano eseguiti solo gli host sessione che devono essere in esecuzione.
11. MyCloudIT fornisce l'accesso al server gateway RDWeb tramite un nome di dominio MyCloudIT.  È anche possibile modificare il mapping dell'URL a un URL personalizzato per gli utenti finali.

## <a name="prerequisites-for-migration"></a>Prerequisiti per la migrazione

1. È necessario avere accesso alla sottoscrizione di Azure che ospita la soluzione Azure RemoteApp corrente.
2. È necessario concedere al portale le autorizzazioni nella sottoscrizione per la migrazione del modello e per creare/modificare la nuova distribuzione MyCloudIT.
3. A causa di una limitazione in Azure RemoteApp, la migrazione di ogni raccolta può essere eseguita solo tre volte.  Se è necessario eseguire la migrazione di una raccolta più di tre volte, è possibile generare un ticket in Azure per aumentare il numero di esportazioni. In alternativa, è possibile contattarci e forniremo l'assistenza necessaria per incrementare il numero di esportazioni nella richiesta ARA.

## <a name="mycloudit-billing"></a>Fatturazione di MyCloudIT

Per informazioni su come stimare e gestire i costi complessivi di Azure, vedere il documento relativo ai [prezzi di MyCloudIT per le soluzioni RemoteApp](https://mcitdocuments.blob.core.windows.net/terms/MyCloudIT_Pricing_Overview.pdf) (PDF).

Per eventuali domande, scrivere all'indirizzo [sales@conexlink.com](mailto:sales@conexlink.com) o guardare il video dimostrativo completo sullo [strumento di migrazione di Azure RemoteApp: MyCloudIT](https://www.youtube.com/watch?v=YQ_1F-JeeLM&t=482s). 


