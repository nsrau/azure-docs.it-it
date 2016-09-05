<properties
   pageTitle="Come configurare gli avvisi di sicurezza | Microsoft Azure"
   description="Informazioni su come configurare gli avvisi di sicurezza per l'estensione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="kgremban"/>

# Come configurare gli avvisi di sicurezza in Azure AD Privileged Identity Management

## Avvisi di sicurezza
Azure Privileged Identity Management (PIM) genera avvisi nel caso di attività sospette o non sicure nel proprio ambiente. Una avviso attivato viene visualizzato nel dashboard di PIM. Selezionare l'avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso.

![Schermata Avvisi di sicurezza del dashboard di PIM][1]



| Avviso | Trigger | Raccomandazione |
| ----- | ------- | -------------- |
| **I ruoli vengono assegnati all'esterno di PIM** | Un amministratore è stato assegnato a un ruolo in modo permanente, all'esterno dell'interfaccia PIM. | Verificare la nuova assegnazione del ruolo. Poiché gli altri servizi possono solo assegnare amministratori permanenti, modificare il valore in un'assegnazione idonea, se necessario. |
| **I ruoli vengono attivati con una frequenza eccessiva** | È stato eseguito un numero eccessivo di riattivazioni dello stesso ruolo nel tempo consentito nelle impostazioni. | Contattare l'utente per verificare il motivo per cui ha attivato il ruolo con una frequenza elevata. Il limite temporale potrebbe essere troppo breve per completare le attività oppure è possibile che l'utente usi script per attivare automaticamente un ruolo. |
| **I ruoli non richiedono l'autenticazione MFA per l'attivazione** | Sono presenti ruoli con impostazioni in cui l'autenticazione MFA non è abilitata. | È richiesta l'autenticazione MFA per i ruoli con i privilegi più elevati, ma è consigliabile abilitare l'autenticazione MFA per l'attivazione di tutti i ruoli. |
| **Gli amministratori non usano i ruoli con privilegi** | Sono presenti amministratori temporanei che non hanno attivato i loro ruoli di recente. | Avviare una verifica di accesso per determinare gli utenti che non necessitano più dell'accesso. |
| **Il numero di amministratori globali presenti è eccessivo** | Sono presenti più amministratori globali di quanti consigliati. | Se il numero di amministratori globali è elevato, è probabile che ottengano più autorizzazioni di quelle necessarie. Trasferire gli utenti a ruoli con privilegi meno elevati o rendere alcuni utenti idonei per il ruolo anziché assegnare i privilegi in modo permanente. |

## Configurare le impostazioni degli avvisi di sicurezza

È possibile personalizzare alcuni avvisi di sicurezza in PIM in linea con gli obiettivi di protezione e l'ambiente. Seguire questa procedura per accedere al pannello impostazioni:

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare **Azure AD Privileged Identity Management** nel dashboard.
2. Selezionare **Managed privileged roles** (Ruoli con privilegi gestiti)> **Impostazioni** > **Impostazioni degli avvisi**.

    ![Passare a impostazioni degli avvisi di sicurezza][2]

### Avviso "I ruoli vengono attivati con una frequenza eccessiva"

Questo avviso viene attivato se un utente attiva lo stesso ruolo con privilegi più volte entro l'intervallo temporale specificato. È possibile configurare il periodo di tempo e il numero di attivazioni.

- **Intervallo di tempo per il rinnovo delle attivazioni**: specificare in giorni, ore, minuti e secondi il periodo per cui si vuole rilevare rinnovi sospetti.

- **Numero di rinnovi di attivazioni**: specificare il numero di attivazioni, da 2 a 100, considerati idonei per un avviso, entro l'intervallo di tempo specificato. È possibile modificare questa impostazione spostando il dispositivo di scorrimento o digitando un numero nella casella di testo.


### Avviso "Il numero di amministratori globali è eccessivo"

Se vengono soddisfatti due criteri diversi ed è possibile configurarli entrambi, PIM attiva l'avviso. È necessario prima raggiungere una determinata soglia di amministratori globali. Una determinata percentuale del totale di assegnazioni di ruoli deve essere rappresentata da amministratori globali. Se viene soddisfatto uno solo di questi criteri, l'avviso non verrà visualizzato.

- **Numero minimo di amministratori globali**: specificare il numero di amministratori globali, da 2 a 100, che si considera un valore non sicuro.

- **Percentuale di amministratori globali**: specificare la percentuale di amministratori che sono amministratori globali, da 0% a 100%, che si considera non sicura nell'ambiente.

### Avviso "Gli amministratori non usano i ruoli con privilegi"

Questo avviso viene attivato se un utente non attiva un ruolo dopo un determinato periodo.

- **Numero di giorni**: specificare il numero di giorni, da 0 a 100, per cui un utente può non attivare un ruolo.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png

<!---HONumber=AcomDC_0824_2016-->