---
title: Mapping di controllo di esempio - progetto di servizi condivisi di ISO 27001-
description: Mapping di controllo dell'esempio di progetto servizi condivisi di ISO 27001 per RBAC e criteri di Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 3ce755b62ce2ba0328852b551225ffa828a6c1bf
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804684"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Mapping di controllo dell'esempio di progetto di servizi condivisi di ISO 27001

L'articolo seguente illustra in dettaglio come servizi di Azure progetti ISO 27001 condiviso blueprint mappe di esempio per i controlli ISO 27001. Per altre informazioni sui controlli, vedere [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

I mapping seguenti sono per la **certificato ISO 27001:2013** controlli. Per passare direttamente a un mapping di controllo specifico, usare il riquadro di spostamento a destra. Molti dei controlli mappati vengono implementate con un [criteri di Azure](../../../policy/overview.md) iniziativa. Per controllare l'iniziativa completa, aprire **criterio** nel portale di Azure e seleziona il **definizioni** pagina. Quindi, individuare e selezionare il  **[anteprima] controllo ISO 27001:2013 consente di controllare e distribuire estensioni di macchina virtuale specifiche per supportare i requisiti di controllo** iniziativa di criteri predefinite.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 la separazione dei compiti

Con un solo proprietario della sottoscrizione di Azure non consente di ridondanza amministrativa. Viceversa, presenza di troppi proprietari di sottoscrizione di Azure, è possibile aumentare la probabilità di una violazione tramite un account del proprietario compromesso. Questo progetto consente di gestire un numero appropriato di proprietari di sottoscrizione di Azure tramite l'assegnazione di due [criteri di Azure](../../../policy/overview.md) definizioni che controlla il numero di proprietari per le sottoscrizioni di Azure. La gestione delle autorizzazioni di proprietario di sottoscrizione può aiutarti a implementare appropriata separazione dei compiti.

- [Anteprima]: Audit minimum number of owners for subscription
- [Anteprima]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>Accesso A.9.1.2 alle reti e servizi di rete

Azure implementa [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) (RBAC) per gestire chi ha accesso alle risorse di Azure. Questo progetto consente di controllare l'accesso alle risorse di Azure tramite l'assegnazione di sette [criteri di Azure](../../../policy/overview.md) definizioni. Questi criteri di controllare l'uso dei tipi di risorse e configurazioni che possono consentire più permissive di accedere alle risorse.
Le risorse di conoscenza che violano tali può criteri della Guida si adotta misure correttive per garantire l'accesso alle risorse di Azure è limitata agli utenti autorizzati.

- [Anteprima]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Anteprima]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Passwords
- [Anteprima]: Audit Linux VM accounts with no passwords
- [Anteprima]: Audit Linux VM allowing remote connections from accounts with no passwords
- Controlla l'uso degli account di archiviazione della versione classica
- Controlla l'uso delle macchine virtuali della versione classica
- Controlla macchine virtuali che non usano dischi gestiti

## <a name="a922-user-access-provisioning"></a>Provisioning dell'accesso utente A.9.2.2

Azure implementa [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) (RBAC) per gestire chi ha accesso alle risorse di Azure. Questo progetto assegna tre [criteri di Azure](../../../policy/overview.md) sfrutta le definizioni per controllare [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) autenticazione per SQL Server e [Service Fabric](../../../../service-fabric/service-fabric-overview.md). L'autenticazione tramite Azure Active Directory Abilita gestione semplificata delle autorizzazioni e gestione centralizzata delle identità degli utenti di database e altri servizi Microsoft. Questo progetto assegna inoltre una definizione di criteri di Azure per controllare l'utilizzo di regole personalizzate di RBAC. Comprendere dove regole RBAC personalizzate sono implementare può agevolare verificare necessità e la corretta esecuzione, come regole RBAC personalizzate sono soggette a errori.

- Controlla il provisioning di un amministratore di Azure Active Directory per SQL Server
- Controlla l'utilizzo di Azure Active Directory per l'autenticazione client in Service Fabric
- Controlla l'uso di ruoli di controllo degli accessi in base al ruolo personalizzati

## <a name="a923-management-of-privileged-access-rights"></a>Gestione A.9.2.3 dei privilegi i diritti di accesso

Questo progetto consente di limitare e controllare i diritti di accesso con privilegi tramite l'assegnazione di quattro [criteri di Azure](../../../policy/overview.md) definizioni per controllare gli account esterni con proprietario e/o account e autorizzazioni di scrittura con il proprietario e/o le autorizzazioni di scrittura che non è abilitata l'autenticazione a più fattori.

- [Anteprima]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Anteprima]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Anteprima]: Audit external accounts with owner permissions on a subscription
- [Anteprima]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Gestione A.9.2.4 segreta di informazioni di autenticazione degli utenti

Questo progetto assegna tre [criteri di Azure](../../../policy/overview.md) definizioni per controllare gli account che non sono abilitata l'autenticazione a più fattori. Multi-factor authentication consente di proteggere gli account anche se viene compromessa un'informazione l'autenticazione. Monitoraggio degli account senza multi-factor authentication abilitata, è possibile identificare gli account che possono essere più possibilità di essere compromessi. Questo progetto assegna inoltre due definizioni di criteri di Azure di VM Linux di controllo delle autorizzazioni di file di password per un avviso se si impostano in modo non corretto. Questa configurazione consente di adottare misure correttive per garantire gli autenticatori non vengano compromessi.

- [Anteprima]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Anteprima]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Anteprima]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Anteprima]: Deploy VM extension to audit Linux VM passwd file permissions
- [Anteprima]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>Revisione A.9.2.5 dei diritti di accesso utente

Azure implementa [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) (RBAC) per consente di gestire chi ha accesso alle risorse in Azure. Nel portale di Azure, è possibile controllare chi può accedere a risorse di Azure e le relative autorizzazioni. Questo progetto assegna quattro [criteri di Azure](../../../policy/overview.md) definizioni per controllare gli account che devono avere una priorità per la revisione, inclusi gli account ammortizzati e gli account esterni con autorizzazioni elevate.

- [Anteprima]: Audit deprecated accounts on a subscription
- [Anteprima]: Audit deprecated accounts with owner permissions on a subscription
- [Anteprima]: Audit external accounts with owner permissions on a subscription
- [Anteprima]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 rimozione o rettifica dei diritti di accesso

Azure implementa [controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) (RBAC) per consente di gestire chi ha accesso alle risorse in Azure. Usando [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e RBAC, è possibile aggiornare i ruoli utente per riflettere le modifiche dell'organizzazione. Quando necessario, gli account possono essere impediti l'accesso (o rimossi), che rimuove immediatamente i diritti di accesso alle risorse di Azure. Questo progetto vengono assegnati due [criteri di Azure](../../../policy/overview.md) definizioni a ammortizzato account che devono essere considerati per la rimozione di controllo.

- [Anteprima]: Audit deprecated accounts on a subscription
- [Anteprima]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>Sistema di gestione Password A.9.4.3

Questo progetto consente di imporre password complessa mediante l'assegnazione di 10 [criteri di Azure](../../../policy/overview.md) definizioni di controllo delle macchine virtuali Windows che non applicano intensità minima e altri requisiti di password. Conoscenza delle macchine virtuali in violazione dei criteri di complessità password consente di intraprendere azioni correttive per assicurarsi che siano conformi ai criteri delle password per tutti gli account utente di macchina virtuale.

- [Anteprima]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Anteprima]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Anteprima]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Anteprima]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Anteprima]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Anteprima]: Audit Windows VM enforces password complexity requirements
- [Anteprima]: Audit Windows VM maximum password age 70 days
- [Anteprima]: Audit Windows VM minimum password age 1 day
- [Anteprima]: Audit Windows VM passwords must be at least 14 characters
- [Anteprima]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Criteri A.10.1.1 sull'uso di controlli crittografici

Questo progetto consente di applicare i criteri all'uso di controlli descritta assegnando 13 [criteri di Azure](../../../policy/overview.md) usano definizioni che applicano controlli specifici descritta e controllo delle impostazioni di crittografia debole.
La comprensione in cui le risorse di Azure abbia le configurazioni di crittografia non ottimali può aiutarti a intraprendere azioni correttive per garantire le risorse sono configurate in conformità con i criteri di protezione delle informazioni. In particolare, i criteri assegnati da questo progetto di richiedono la crittografia per l'account di archiviazione blob e gli account di archiviazione data lake; Richiedi crittografia trasparente dei dati nel database SQL. controllo della crittografia mancante in account di archiviazione, database SQL, dischi delle macchine virtuali e le variabili di account di automazione; Controllare le connessioni non sicure per gli account di archiviazione, App per le funzioni, App Web, App per le API e Cache Redis. controllo della crittografia di password vulnerabili macchina virtuale; controlli e comunicazione non crittografata di Service Fabric.

- [Anteprima]: Audit HTTPS only access for a Function App
- [Anteprima]: Audit HTTPS only access for a Web Application
- [Anteprima]: Audit HTTPS only access for an API App
- [Anteprima]: Audit missing blob encryption for storage accounts
- [Anteprima]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  Crittografian
- [Anteprima]: Audit Windows VM should not store passwords using reversible encryption
- [Anteprima]: Monitor unencrypted SQL database in Azure Security Center
- [Anteprima]: Monitor unencrypted VM Disks in Azure Security Center
- Controlla l'abilitazione della crittografia delle variabili dell'account di Automazione
- Controlla l'abilitazione delle sole connessioni sicure alla cache Redis
- Controlla il trasferimento sicuro negli account di archiviazione
- Controlla l'impostazione della proprietà ClusterProtectionLevel su EncryptAndSign in Service Fabric
- Controllare lo stato di Transparent Data Encryption

## <a name="a1241-event-logging"></a>Registrazione degli eventi A.12.4.1

Questo progetto consente di verificare che gli eventi di sistema vengono registrati tramite l'assegnazione di sette [criteri di Azure](../../../policy/overview.md) definizioni che controlla le impostazioni del log in risorse di Azure. Un criterio assegnato controlla anche se le macchine virtuali non inviano i log a un'area di lavoro di analitica di log specificato.

- [Anteprima]: Distribuzione dell'agente di dipendenza - immagini (sistema operativo VM) rimossa dall'elenco di controllo
- [Anteprima]: Distribuzione dell'agente di dipendenza nel VMSS - immagini (sistema operativo VM) rimossa dall'elenco di controllo
- [Anteprima]: La distribuzione dell'agente di Log Analitica - immagini (sistema operativo VM) rimossa dall'elenco di controllo
- [Anteprima]: Distribuzione dell'agente di controllo Log Analitica nei set di scalabilità - immagine (sistema operativo VM) non in elenco
- [Anteprima]: Monitor unaudited SQL database in Azure Security Center
- Audit diagnostic setting (Controllare le impostazioni di diagnostica)
- Controlla le impostazioni di controllo a livello del server SQL

## <a name="a121-management-of-technical-vulnerabilities"></a>Gestione A.12.1 delle vulnerabilità tecniche

Questo progetto consente di gestire le vulnerabilità del sistema di informazioni tramite l'assegnazione di cinque [criteri di Azure](../../../policy/overview.md) definizioni che consentono di monitorare gli aggiornamenti del sistema mancanti, vulnerabilità del sistema operativo, le vulnerabilità SQL e macchina virtuale vulnerabilità. Queste informazioni forniscono informazioni in tempo reale sullo stato di protezione delle risorse distribuite e possono aiutarti a priorità delle azioni di correzione.

- [Anteprima]: Monitor missing Endpoint Protection in Azure Security Center
- [Anteprima]: Monitor missing system updates in Azure Security Center
- [Anteprima]: Monitor OS vulnerabilities in Azure Security Center
- [Anteprima]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Anteprima]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>Restrizioni A.12.6.2 Installazione software

Controllo delle applicazioni adattivo è una soluzione dal Centro sicurezza di Azure che consente di controllare quali applicazioni possono eseguire in macchine virtuali in Azure. Questo progetto consente di assegnare una definizione di criteri di Azure che monitora le modifiche apportate al set di applicazioni consentite. Restrizioni per l'installazione del software possono aiutarti a ridurre la probabilità di introduzione di vulnerabilità del software.

- [Anteprima]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>Controlli di rete A.13.1.1

Questo progetto consente di gestire e controllare le reti tramite l'assegnazione di un [criteri di Azure](../../../policy/overview.md) definizione che consente di monitorare gruppi di sicurezza di rete con regole permissive. Le regole troppo permissive possono consentire l'accesso alla rete non intenzionali e devono essere rivisti.

- [Anteprima]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>Procedure e criteri di trasferimento delle informazioni A.13.2.1

Il progetto consente di verificare che il trasferimento di informazioni con servizi di Azure è protetto tramite l'assegnazione di due [criteri di Azure](../../../policy/overview.md) definizioni per controllare le connessioni non sicure per gli account di archiviazione e la Cache Redis.

- Controlla l'abilitazione delle sole connessioni sicure alla cache Redis
- Controlla il trasferimento sicuro negli account di archiviazione

## <a name="a1413-protecting-application-services-transactions"></a>Servizi delle applicazioni di protezione A.14.1.3 transazioni

Questo progetto consente di proteggere le risorse di sistema di informazioni tramite l'assegnazione di tre [criteri di Azure](../../../policy/overview.md) definizioni che consentono di monitorare gli endpoint non protetti, applicazioni e gli account di archiviazione. Gli endpoint e le applicazioni che non sono protette da un firewall e gli account di archiviazione con accesso illimitato possono consentire l'accesso non autorizzato alle informazioni contenute all'interno del sistema informativo.

- [Anteprima]: Monitor unprotected network endpoints in Azure Security Center
- [Anteprima]: Monitor unprotected web application in Azure Security Center
- Controlla l'accesso di rete senza restrizioni agli account di archiviazione

## <a name="a1613-reporting-information-security-weaknesses"></a>Problemi di sicurezza delle informazioni di A.16.1.3 Reporting

Questo progetto consente di mantenere la consapevolezza delle vulnerabilità del sistema tramite l'assegnazione di cinque [criteri di Azure](../../../policy/overview.md) definizioni che monitora le vulnerabilità, lo stato delle patch e gli avvisi di malware nel Centro sicurezza di Azure. Centro sicurezza di Azure offre funzionalità di report che consentono di disporre in tempo reale informazioni dettagliate sullo stato di sicurezza delle risorse di Azure distribuite.

- [Anteprima]: Monitor missing Endpoint Protection in Azure Security Center
- [Anteprima]: Monitor missing system updates in Azure Security Center
- [Anteprima]: Monitor OS vulnerabilities in Azure Security Center
- [Anteprima]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Anteprima]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il mapping di controllo del progetto di servizi condivisi di ISO 27001, vedere gli articoli seguenti per informazioni sull'architettura e la modalità di distribuzione in questo esempio:

> [!div class="nextstepaction"]
> [Progetto di servizi condivisi di ISO 27001 - Panoramica](./index.md)
> [ISO 27001 Shared Services blueprint - distribuzione passaggi](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).