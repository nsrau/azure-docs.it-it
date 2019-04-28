---
title: Come rinnovare i certificati X.509 nel servizio Device Provisioning in hub IoT di Azure | Microsoft Docs
description: Come rinnovare i certificati X.509 in un'istanza del servizio di provisioning di dispositivi
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8cf5f262a758efe08ad73e2d8066ad4b736e76d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626954"
---
# <a name="how-to-roll-x509-device-certificates"></a>Come rinnovare i certificati X.509 dei dispositivi

Durante il ciclo di vita di una soluzione IoT, è necessario rinnovare i certificati. Due dei principali motivi del rinnovo sono costituiti da una possibile violazione della sicurezza e dalle scadenze dei certificati. 

Il rinnovo dei certificati è una procedura consigliata per la sicurezza che consente di proteggere il sistema in caso di violazione. Come parte della [metodologia di presunzione delle violazioni](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), Microsoft sostiene che, parallelamente alle misure preventive, sia necessario mettere in atto processi di sicurezza reattivi. In questi processi di sicurezza deve essere incluso anche il rinnovo dei certificati dei dispositivi. La frequenza di rinnovo dei certificati dipende dalle esigenze di sicurezza della soluzione in uso. I clienti di soluzioni con dati altamente sensibili potrebbero rinnovare i certificati ogni giorno, mentre altri potrebbero rinnovarli ogni due anni.

Il rinnovo dei certificati dei dispositivi comporta l'aggiornamento del certificato archiviato nel dispositivo e dell'hub IoT. Al termine dell'aggiornamento, il dispositivo può procedere a un nuovo provisioning nell'hub IoT usando la normale funzione di [provisioning automatico](concepts-auto-provisioning.md) con il servizio Device Provisioning.


## <a name="obtain-new-certificates"></a>Ottenere nuovi certificati

Esistono diversi modi per ottenere nuovi certificati per i dispositivi IoT. È ad esempio possibile ottenere certificati dalla factory dei dispositivi, generare certificati in modo autonomo oppure richiedere a terzi di gestire per proprio conto la creazione di certificati. 

I certificati si assegnano reciprocamente una firma in modo da formare una catena da un certificato dell'autorità di certificazione (CA) radice a un [certificato foglia](concepts-security.md#end-entity-leaf-certificate). Un certificato di firma è il certificato usato per firmare il certificato foglia in fondo alla catena. Può essere un certificato della CA radice o un certificato intermedio nella catena. Per altre informazioni, vedere [Certificati X.509](concepts-security.md#x509-certificates).
 
Esistono due modi diversi per ottenere un certificato di firma. Il primo, consigliato per i sistemi di produzione, consiste nell'acquistare un certificato di firma da una CA radice. In questo modo la sicurezza viene concatenata fino a un'origine attendibile. 

Il secondo modo consiste nel creare i propri certificati X.509 usando uno strumento come OpenSSL. Questa è un'ottima soluzione per eseguire il test dei certificati X.509 ma offre poche garanzie in termini di sicurezza. È consigliabile usare questo approccio solo a scopo di test a meno che non si preveda di agire come provider di CA indipendente.
 

## <a name="roll-the-certificate-on-the-device"></a>Rinnovare il certificato nel dispositivo

I certificati di un dispositivo devono sempre essere archiviati in un luogo sicuro, ad esempio in un [modulo di protezione hardware](concepts-device.md#hardware-security-module). La modalità di rinnovo dei certificati dei dispositivi dipende da come sono stati creati e installati nei dispositivi per la prima volta. 

Se si sono ottenuti i certificati da una terza parte, è necessario verificare in che modo è previsto il rinnovo dei certificati. Il processo può essere incluso in un accordo con la terza parte oppure può essere offerto come servizio separato. 

Se si gestiscono i certificati dei dispositivi in modo autonomo, sarà necessario creare una propria pipeline per l'aggiornamento dei certificati. Assicurarsi che i certificati foglia, sia vecchi che nuovi, abbiano lo stesso nome comune (CN). Con lo stesso CN, il dispositivo può procedere a un nuovo provisioning senza creare un record di registrazione duplicato. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Rinnovare il certificato nell'hub IoT

Il certificato di un dispositivo può essere aggiunto manualmente a un hub IoT. Il certificato può anche essere gestito automaticamente tramite un'istanza del servizio di provisioning di dispositivi. In questo articolo si presuppone che per supportare il provisioning automatico venga usata un'istanza del servizio di provisioning.

Quando è inizialmente sottoposto a provisioning automatico, il dispositivo viene avviato e contatta il servizio di provisioning. Quest'ultimo risponde eseguendo un controllo di identità prima di creare un'identità del dispositivo in un hub IoT con il certificato foglia del dispositivo come credenziale. Il servizio di provisioning indica quindi al dispositivo l'hub IoT a cui è assegnato e il dispositivo usa il proprio certificato foglia per autenticarsi e connettersi all'hub IoT. 

Dopo l'assegnazione di un nuovo certificato foglia, il dispositivo non può più connettersi all'hub IoT perché usa un nuovo certificato, mentre l'hub IoT riconosce solo il dispositivo con il certificato precedente. Il tentativo di connessione del dispositivo ha quindi come risultato un errore di connessione "non autorizzata". Per risolvere questo errore, è necessario aggiornare la voce di registrazione per il dispositivo in base al nuovo certificato foglia. Al termine del nuovo provisioning del dispositivo, il servizio di provisioning può aggiornare di conseguenza le informazioni del registro dei dispositivi dell'hub IoT. 

Una possibile eccezione a questo errore di connessione è data da uno scenario in cui è stato creato un [gruppo di registrazioni](concepts-service.md#enrollment-group) per il dispositivo nel servizio di provisioning. In questo caso, anche se non si rinnova il certificato radice o intermedio nella catena di certificati del dispositivo, quest'ultimo viene riconosciuto se il nuovo certificato fa parte della catena di certificati definita nel gruppo di registrazioni. Se questo scenario si presenta come reazione a una violazione della sicurezza, è necessario inserire nell'elenco di elementi non consentiti almeno i certificati dei dispositivi nel gruppo che sono stati presumibilmente violati. Per altre informazioni, vedere [Inserire dispositivi specifici in un gruppo di registrazione nell'elenco di elementi non consentiti](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

L'aggiornamento delle voci di registrazione per i certificati rinnovati viene eseguito nella pagina **Gestisci registrazioni**. Per accedere alla pagina, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza del servizio Device Provisioning in hub IoT che contiene la voce di registrazione per il dispositivo.

2. Fare clic su **Gestisci registrazioni**.

    ![Gestire le registrazioni](./media/how-to-roll-certificates/manage-enrollments-portal.png)


La modalità di gestione dell'aggiornamento della voce di registrazione dipende dall'uso di registrazioni singole o di gruppo. Anche le procedure consigliate variano a seconda che i certificati vengano rinnovati a causa di una violazione della sicurezza o della scadenza. Le sezioni seguenti descrivono come gestire questi aggiornamenti.


## <a name="individual-enrollments-and-security-breaches"></a>Registrazioni singole e violazioni della sicurezza

Se si rinnovano i certificati in risposta a una violazione della sicurezza, è consigliabile usare l'approccio seguente che consente di eliminare immediatamente il certificato corrente:

1. Fare clic su **Registrazioni singole** e quindi sulla voce dell'ID di registrazione nell'elenco. 

2. Fare clic sul pulsante **Elimina certificato corrente** e quindi sull'icona della cartella per selezionare il nuovo certificato da caricare per la voce di registrazione. Al termine, fare clic su **Salva**.

    Questi passaggi devono essere completati sia per il certificato primario sia per quello secondario, se sono entrambi compromessi.

    ![Gestire le registrazioni singole](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Una volta eliminato il certificato compromesso dal servizio di provisioning, il certificato può essere ancora usato per stabilire le connessioni dei dispositivi agli hub loT, purchè esista una registrazione del dispositivo. È possibile risolvere questo problema in due modi: 

    Il primo consiste nel passare manualmente all'hub IoT ed eliminare immediatamente la registrazione del dispositivo associata al certificato compromesso. Quando il dispositivo esegue nuovamente il provisioning con un certificato aggiornato, viene creata una nuova registrazione del dispositivo.     

    ![Rimuovere la registrazione del dispositivo nell'hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Il secondo consiste nell'usare il supporto reprovisioning per eseguire il reprovisioning del dispositivo allo stesso hub IoT. Questo approccio può essere usato per sostituire il certificato per la registrazione del dispositivo sull'hub loT. Per altre informazioni, vedere [Come effettuare il reprovisioning dei dispositivi](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Registrazioni singole e scadenza del certificato

Se si rinnovano i certificati per gestirne la scadenza, è consigliabile usare la configurazione del certificato secondario come indicato di seguito per ridurre il tempo di inattività per i dispositivi che tentano il provisioning.

In un secondo momento, quando il certificato secondario è prossimo alla scadenza e deve essere rinnovato, è possibile passare alla configurazione del certificato primario. La rotazione tra il certificato primario e quello secondario consente così di ridurre il tempo di inattività per i dispositivi che tentano il provisioning.


1. Fare clic su **Registrazioni singole** e quindi sulla voce dell'ID di registrazione nell'elenco. 

2. Fare clic su **Certificato secondario** e quindi sull'icona della cartella per selezionare il nuovo certificato da caricare per la voce di registrazione. Fare clic su **Save**.

    ![Gestire le registrazioni singole usando il certificato secondario](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. In un secondo momento, quando il certificato primario è scaduto, tornare indietro ed eliminare tale certificato facendo clic sul pulsante **Elimina certificato corrente**.

## <a name="enrollment-groups-and-security-breaches"></a>Gruppi di registrazioni e violazioni della sicurezza

Per aggiornare una registrazione di gruppo in risposta a una violazione della sicurezza, è consigliabile usare uno degli approcci seguenti che consentono di eliminare immediatamente la CA radice corrente o un certificato intermedio.

#### <a name="update-compromised-root-ca-certificates"></a>Aggiornare i certificati della CA radice compromessi

1. Fare clic sulla scheda **Certificati** per l'istanza del servizio di provisioning di dispositivi.

2. Fare clic sul certificato compromesso nell'elenco e quindi sul pulsante **Elimina**. Confermare l'eliminazione digitando il nome del certificato e fare clic su **OK**. Ripetere questa procedura per tutti i certificati compromessi.

    ![Eliminare un certificato della CA radice](./media/how-to-roll-certificates/delete-root-cert.png)

3. Seguire la procedura illustrata in [Configurare i certificati della CA verificati](how-to-verify-certificates.md) per aggiungere e verificare i nuovi certificati della CA radice.

4. Fare clic sulla scheda **Gestisci registrazioni** per l'istanza del servizio di provisioning di dispositivi e quindi sull'elenco **Gruppi di registrazioni**. Fare clic sul nome del gruppo di registrazioni nell'elenco.

5. Fare clic su **Certificato CA** e selezionare il nuovo certificato della CA radice. Fare quindi clic su **Salva**. 

    ![Selezionare il nuovo certificato della CA radice](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Una volta eliminato il certificato compromesso dal servizio di provisioning, il certificato può essere ancora usato per stabilire connessioni dei dispositivi agli hub loT, purchè esista una registrazione del dispositivo. È possibile risolvere questo problema in due modi: 

    Il primo consiste nel passare manualmente all'hub IoT ed eliminare immediatamente la registrazione del dispositivo associata al certificato compromesso. Quando i dispositivi eseguono nuovamente il provisioning con i certificati aggiornati, viene quindi creata una nuova registrazione del dispositivo per ognuno di essi.     

    ![Rimuovere la registrazione del dispositivo nell'hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Il secondo consiste nell'usare il supporto reprovisioning per eseguire il reprovisioning del dispositivo allo stesso hub IoT. Questo approccio può anche essere usato per sostituire i certificati per le registrazioni del dispositivo sull'hub loT. Per altre informazioni, vedere [Come effettuare il reprovisioning dei dispositivi](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Aggiornare i certificati intermedi compromessi

1. Fare clic su **Gruppi di registrazioni** e quindi sul nome del gruppo nell'elenco. 

2. Fare clic su **Certificato intermedio** e su **Elimina certificato corrente**. Fare clic sull'icona della cartella per passare al nuovo certificato intermedio da caricare per il gruppo di registrazioni. Al termine, fare clic su **Salva**. Questi passaggi devono essere completati sia per il certificato primario sia per quello secondario, se sono entrambi compromessi.

    Il nuovo certificato intermedio deve essere firmato da un certificato della CA radice verificato che sia già stato aggiunto al servizio di provisioning. Per altre informazioni, vedere [Certificati X.509](concepts-security.md#x509-certificates).

    ![Gestire le registrazioni singole](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Una volta eliminato il certificato compromesso dal servizio di provisioning, il certificato può essere ancora usato per stabilire connessioni dei dispositivi agli hub loT, purchè esista una registrazione del dispositivo. È possibile risolvere questo problema in due modi: 

    Il primo consiste nel passare manualmente all'hub IoT ed eliminare immediatamente la registrazione del dispositivo associata al certificato compromesso. Quando i dispositivi eseguono nuovamente il provisioning con i certificati aggiornati, viene quindi creata una nuova registrazione del dispositivo per ognuno di essi.     

    ![Rimuovere la registrazione del dispositivo nell'hub IoT](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Il secondo consiste nell'usare il supporto reprovisioning per eseguire il reprovisioning del dispositivo allo stesso hub IoT. Questo approccio può anche essere usato per sostituire i certificati per le registrazioni del dispositivo sull'hub loT. Per altre informazioni, vedere [Come effettuare il reprovisioning dei dispositivi](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Gruppi di registrazione e scadenza del certificato

Se si rinnovano i certificati per gestirne la scadenza, è consigliabile usare la configurazione del certificato secondario come indicato di seguito per azzerare il tempo di inattività per i dispositivi che tentano il provisioning.

In un secondo momento, quando il certificato secondario è prossimo alla scadenza e deve essere rinnovato, è possibile passare alla configurazione del certificato primario. La rotazione tra il certificato primario e quello secondario consente così di azzerare il tempo di inattività per i dispositivi che tentano il provisioning. 

#### <a name="update-expiring-root-ca-certificates"></a>Aggiornare i certificati della CA radice in scadenza

1. Seguire la procedura illustrata in [Configurare i certificati della CA verificati](how-to-verify-certificates.md) per aggiungere e verificare i nuovi certificati della CA radice.

2. Fare clic sulla scheda **Gestisci registrazioni** per l'istanza del servizio di provisioning di dispositivi e quindi sull'elenco **Gruppi di registrazioni**. Fare clic sul nome del gruppo di registrazioni nell'elenco.

3. Fare clic su **Certificato CA** e selezionare il nuovo certificato della CA radice nella configurazione **Certificato secondario**. Fare quindi clic su **Salva**. 

    ![Selezionare il nuovo certificato della CA radice](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. In un secondo momento, quando il certificato primario è scaduto, fare clic sulla scheda **Certificati** per l'istanza del servizio di provisioning di dispositivi. Fare clic sul certificato scaduto nell'elenco e quindi sul pulsante **Elimina**. Confermare l'eliminazione digitando il nome del certificato e fare clic su **OK**.

    ![Eliminare un certificato della CA radice](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Aggiornare i certificati intermedi in scadenza


1. Fare clic su **Gruppi di registrazioni** e quindi sul nome del gruppo nell'elenco. 

2. Fare clic su **Certificato secondario** e quindi sull'icona della cartella per selezionare il nuovo certificato da caricare per la voce di registrazione. Fare clic su **Save**.

    Il nuovo certificato intermedio deve essere firmato da un certificato della CA radice verificato che sia già stato aggiunto al servizio di provisioning. Per altre informazioni, vedere [Certificati X.509](concepts-security.md#x509-certificates).

   ![Gestire le registrazioni singole usando il certificato secondario](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. In un secondo momento, quando il certificato primario è scaduto, tornare indietro ed eliminare tale certificato facendo clic sul pulsante **Elimina certificato corrente**.


## <a name="reprovision-the-device"></a>Eseguire un nuovo provisioning del dispositivo

Dopo che il certificato è stato rinnovato nel dispositivo e nel servizio di provisioning di dispositivi, il dispositivo può procedere a un nuovo provisioning contattando il servizio di provisioning di dispositivi. 

Un modo semplice per programmare un nuovo provisioning per un dispositivo consiste nel configurare il dispositivo in modo da contattare il servizio di provisioning per passare attraverso il flusso di provisioning, nel caso in cui il dispositivo riceva un errore di connessione "non autorizzata" durante il tentativo di connettersi all'hub IoT.

Un altro modo è quello di mantenere la validità dei certificati vecchi e nuovi per un breve intervallo di tempo e usare l'hub IoT per inviare un comando ai dispositivi in modo da ripeterne la registrazione tramite il servizio di provisioning per aggiornare le informazioni sulla relativa connessione all'hub IoT. Poiché ogni dispositivo può elaborare i comandi in modo diverso, è necessario programmare il dispositivo affinché sappia come agire quando viene richiamato il comando. Esistono diversi modi per inviare comandi al dispositivo tramite l'hub IoT ed è consigliabile usare [metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md) o [processi](../iot-hub/iot-hub-devguide-jobs.md) per avviare l'operazione.

Al termine del nuovo provisioning, i dispositivi saranno in grado di connettersi all'hub IoT usando i nuovi certificati.


## <a name="blacklist-certificates"></a>Inserire i certificati nell'elenco di quelli non consentiti

In risposta a una violazione della sicurezza, può essere necessario inserire un certificato di dispositivo nell'elenco di quelli non consentiti. Per effettuare questa operazione, disabilitare la voce di registrazione del dispositivo/certificato di destinazione. Per altre informazioni, vedere come inserire i dispositivi nell'elenco di elementi non consentiti nell'articolo [Gestire la revoca delle registrazioni](how-to-revoke-device-access-portal.md).

Dopo che un certificato è stato incluso in una voce di registrazione disabilitata, qualsiasi tentativo di registrazione in un hub IoT tramite tale certificato non avrà esito positivo anche se è abilitato come parte di un'altra voce di registrazione.
 



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui certificati X.509 nel servizio Device Provisioning, vedere [Sicurezza](concepts-security.md). 
- Per informazioni su come eseguire una verifica del possesso per i certificati della CA X.509 con il servizio Device Provisioning in hub IoT di Azure, vedere [Come eseguire la verifica dei certificati](how-to-verify-certificates.md).
- Per informazioni su come usare il portale per creare un gruppo di registrazione, vedere [Gestione delle registrazioni dei dispositivi con il portale di Azure](how-to-manage-enrollments.md).










