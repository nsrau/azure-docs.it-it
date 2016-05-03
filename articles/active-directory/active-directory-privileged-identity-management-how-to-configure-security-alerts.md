<properties
   pageTitle="Come configurare gli avvisi di sicurezza | Microsoft Azure"
   description="Informazioni su come configurare gli avvisi di sicurezza per l'estensione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Come configurare gli avvisi di sicurezza in Azure AD Privileged Identity Management

## Avvisi di sicurezza
Azure Privileged Identity Management (PIM) genera gli avvisi seguenti, che possono essere visualizzati nella sezione Avvisi del dashboard di PIM.

| Avviso | Trigger | Raccomandazione |
| ----- | ------- | -------------- |
| **Attivazione permanente** | Un amministratore è stato assegnato a un ruolo in modo permanente, all'esterno di PIM. | Rivedere l'assegnazione del nuovo ruolo e modificarlo in ruolo temporaneo, se necessario. |
| **Rinnovo attivazione sospetto di ruoli con privilegi** | È stato eseguito un numero eccessivo di riattivazioni dello stesso ruolo nel tempo consentito nelle impostazioni. | Contattare l'utente per assicurarsi che possa attivare il ruolo correttamente. |
| **Autenticazione debole configurata per l'attivazione del ruolo** | Sono presenti ruoli senza MFA nelle impostazioni. | È consigliabile richiedere l'autenticazione MFA per l'attivazione di tutti i ruoli. |
| **Numero eccessivo di amministratori** | Sono presenti amministratori temporanei che non hanno attivato i loro ruoli di recente. | Rimuovere le assegnazioni dei ruoli non più necessari. |
| **Numero eccessivo di amministratori globali** | Sono presenti più amministratori globali di quanti consigliati. | Rimuovere le assegnazioni dei ruoli che non sono più necessarie o modificare alcuni ruoli in ruoli temporanei. |

## Configurare le impostazioni degli avvisi di sicurezza

### Avviso "Rinnovi sospetti dell'attivazione di ruoli con privilegi"

Configurare le impostazioni **Intervallo di tempo per il rinnovo delle attivazioni** e **Numero di rinnovi** per specificare quando attivare l'avviso.

1. Nella sezione **Attività** del dashboard selezionare **Avvisi di sicurezza**. Verrà visualizzato il pannello **Avvisi di sicurezza attivi**.
2. Fare clic su **Impostazioni**.
3. Impostare il valore di **Intervallo di tempo rinnovi attivazione** regolando il dispositivo di scorrimento o immettendo il numero di minuti nel campo di testo. Il valore massimo è 100.
4. Impostare il valore di **Numero rinnovi attivazione** nell'intervallo di tempo dei rinnovi dell'attivazione regolando il dispositivo di scorrimento o immettendo il numero di rinnovi nel campo di testo. Il valore massimo è 100.
5. Fare clic su **Save**.

### Avviso "Numero eccessivo di amministratori"
1. Nella sezione **Attività** del dashboard selezionare **Avvisi di sicurezza**. Verrà visualizzato il pannello **Avvisi di sicurezza attivi**.
2. Fare clic su **Impostazioni**.
3. Selezionare il numero di giorni consentiti senza attivazione del ruolo regolando il dispositivo di scorrimento o immettendo il numero di giorni nel campo di testo.
4. Fare clic su **Save**.

### Avviso "Numero eccessivo di amministratori globali"

Esistono due impostazioni che possono attivare questo avviso:
- **Numero minimo di amministratori globali** attiva l'avviso se sono presenti più amministratori del numero consentito.
- **Percentuale di amministratori globali** attiva l'avviso se la percentuale di amministratori globali è maggiore di quella consentita dalle impostazioni.

1. Nella sezione **Attività** del dashboard selezionare **Avvisi di sicurezza**. Verrà visualizzato il pannello **Avvisi di sicurezza attivi**.
2. Fare clic su **Impostazioni**.
3. Impostare il campo **Numero minimo di amministratori globali** regolando il dispositivo di scorrimento o immettendo il numero nel campo di testo.
4. Impostare **Percentuale di amministratori globali** regolando il dispositivo di scorrimento o immettendo la percentuale nel campo di testo.
5. Fare clic su **Save**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->