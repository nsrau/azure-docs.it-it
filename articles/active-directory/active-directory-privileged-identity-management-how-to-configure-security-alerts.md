<properties
   pageTitle="Azure Privileged Identity Management: Come configurare gli avvisi di sicurezza"
   description="Informazioni su come configurare gli avvisi di sicurezza per l'estensione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management: Come configurare gli avvisi di sicurezza
## Panoramica degli avvisi di sicurezza
Azure Privileged Identity Management consente di configurare gli avvisi seguenti. Gli avvisi di sicurezza possono essere visualizzati nella sezione degli avvisi del dashboard PIM.

| Avviso | Trigger |
| ------------- | ------------- |
| **Sospetto attacco di attivazione permanente** | Un amministratore ha attivato il proprio ruolo temporaneo all'esterno di PIM. |
| **Rinnovo attivazione sospetto di ruoli con privilegi** | È stato eseguito un numero eccessivo di riattivazioni dello stesso ruolo nel tempo consentito nelle impostazioni. |
| **Uso sospetto di utente Amministratore globale honeytoken** | È stato rilevato l'uso di un utente "honeypot".|
| **Autenticazione debole configurata per l'attivazione del ruolo** | Sono presenti ruoli senza MFA nelle impostazioni. |
| **Gli amministratori ridondanti aumentano la superficie di attacco** | Sono presenti amministratori temporanei che non hanno attivato i ruoli nel numero di giorni definiti nelle impostazioni. |
| **Un numero elevato di amministratori globali aumenta la superficie di attacco** | Sono presenti più amministratori globali di quanto consentito nelle impostazioni. |

## Configurazione degli avvisi di sicurezza

### Configurare l'avviso "Rinnovo attivazione sospetto di ruoli con privilegi"
1. Nella sezione **Attività** del dashboard selezionare **Avvisi di sicurezza**. Verrà visualizzato il pannello **Avvisi di sicurezza attivi**.
2. Fare clic su **Impostazioni**.
3. Impostare il valore di **Intervallo di tempo rinnovi attivazione** regolando il dispositivo di scorrimento o immettendo il numero di minuti nel campo di testo. Il numero massimo consentito è 100.
4. Impostare il valore di **Numero rinnovi attivazione** nell'intervallo di tempo dei rinnovi dell'attivazione regolando il dispositivo di scorrimento o immettendo il numero di rinnovi nel campo di testo. Il numero massimo di rinnovi è 100.
5. Fare clic su **Save**.

### Configurare l'avviso "Gli amministratori ridondanti aumentano la superficie di attacco"
1. Nella sezione **Attività** del dashboard selezionare **Avvisi di sicurezza**. Verrà visualizzato il pannello **Avvisi di sicurezza attivi**.
2. Fare clic su **Impostazioni**.
3. Selezionare il numero di giorni consentiti senza attivazione del ruolo regolando il dispositivo di scorrimento o immettendo il numero di giorni nel campo di testo.
4. Fare clic su **Save**.

### Configurare l'avviso "Un numero elevato di amministratori globali aumenta la superficie di attacco"

Esistono due impostazioni che possono attivare l'avviso. Se sono presenti più amministratori del consentito, l'avviso verrà attivato dal numero minimo di amministratori globali. L'avviso verrà attivato anche se la percentuale di amministratori globali nel numero totale di amministratori è superiore alla percentuale specificata nelle impostazioni.

1. Nella sezione **Attività** del dashboard selezionare **Avvisi di sicurezza**. Verrà visualizzato il pannello **Avvisi di sicurezza attivi**.
2. Fare clic su **Impostazioni**.
3. Impostare il campo **Numero minimo di amministratori globali** regolando il dispositivo di scorrimento o immettendo il numero nel campo di testo.
4. Impostare il campo **Percentuale di amministratori globali** regolando il dispositivo di scorrimento o immettendo il numero nel campo di testo.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->