<properties
   pageTitle="Come avviare una verifica della sicurezza | Microsoft Azure"
   description="Informazioni su come creare una verifica della sicurezza per le identità con privilegi con l'applicazione Azure Privileged Identity Management."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Come avviare una verifica della sicurezza in Azure AD Privileged Identity Management

Le assegnazioni dei ruoli diventano "obsolete" quando gli utenti hanno accessi con privilegi di cui non necessitano più. Per ridurre il rischio associato alle assegnazioni dei ruoli obsolete, gli amministratori dei ruoli con privilegi devono esaminare periodicamente i ruoli assegnati agli utenti. Questo documento illustra i passaggi per l'avvio di una verifica della sicurezza in Azure AD Privileged Identity Management (PIM).

## Avviare una verifica della sicurezza
> [AZURE.NOTE] Se l'applicazione PIM non è stata aggiunta al dashboard nel portale di Azure, vedere i passaggi descritti in [Introduzione ad Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)

Nella pagina principale dell'applicazione PIM sono disponibili tre opzioni per avviare una verifica della sicurezza:

- **Verifiche della sicurezza** > **Verifica** > pulsante **Verifica**
- **Ruoli** > pulsante **Verifica**
- Selezionare il ruolo da verificare dall'elenco dei ruoli > pulsante **Verifica**

Quando si fa clic sul pulsante **Verifica**, vengono visualizzati i pannelli **Inizia a verificare un ruolo** e **Selezionare un ruolo da verificare**. L'opzione **Selezionare un ruolo da verificare** è già selezionata.

### Selezionare il ruolo da verificare

1. Selezionare il ruolo dall'elenco dei ruoli nel pannello **Selezionare un ruolo da verificare**. È possibile scegliere solo un ruolo per volta. Il pannello **Seleziona ruolo da rivedere** verrà sostituito dal pannello **Seleziona revisori**. Per la selezione dei revisori sono disponibili due opzioni:
  - Selezione di se stessi come revisori: usare questa opzione per visualizzare un'anteprima del funzionamento delle verifiche della sicurezza senza coinvolgere altri amministratori.
  - Verifica automatica dei membri dei ruoli: usare questa opzione per fare in modo che gli utenti verifichino le proprie assegnazioni dei ruoli.
2. Selezionare una di queste due opzioni per iniziare a esaminare i dettagli della revisione. Verrà visualizzato il pannello **Modifica impostazioni predefinite**.

### Revisione di tipo "Io"

Se è stata selezionata l'opzione "Io" come revisore, passare alla revisione della sicurezza. Per altre informazioni su come completare la revisione, vedere [Azure Privileged Identity Management: Come eseguire una revisione della sicurezza](active-directory-privileged-identity-management-how-to-perform-security-review.md).

### Verifica automatica dei membri dei ruoli

Se si sceglie di fare in modo che gli utenti verifichino le proprie assegnazioni dei ruoli, seguire questa procedura per impostare la verifica e inviare le notifiche.

1. Denominare la revisione immettendo il nome della revisione nel campo **Nome**. Assegnare alla verifica un nome univoco descrittivo che consenta di tenerne facilmente traccia.
2. Immettere una data di inizio della revisione nel campo **Data inizio**.
3. Immettere una data di fine della revisione nel campo **Data fine**. Sono elencati di seguito alcuni aspetti da considerare quando si imposta la data di fine della revisione:
  - Numero di persone da sottoporre a revisione
  - Tempo necessario agli utenti per l'aggiunta dell'applicazione PIM nel portale di Azure e il completamento della verifica
4. Fare clic su **OK** nel pannello **Modifica impostazioni predefinite**. Il pannello verrà chiuso.
5. Fare clic su **OK** nel pannello **Inizia a verificare un ruolo**. Il pannello verrà chiuso. Nel menu principale del portale di Azure verrà visualizzata una notifica. Aggiornare il dashboard facendo clic sul pulsante **Aggiorna**. La verifica della sicurezza verrà visualizzata nella sezione **Verifiche della sicurezza**.
6. Inviare una notifica agli utenti del ruolo in cui viene richiesto di aggiungere l'applicazione PIM e di [verificare il proprio accesso amministrativo](active-directory-privileged-identity-management-how-to-perform-security-review.md).  

## Gestire la verifica della sicurezza

È possibile tenere traccia dello stato di avanzamento delle verifiche eseguite dai revisori nella sezione Verifiche della sicurezza del dashboard di Azure AD PIM. Nessun diritto di accesso verrà modificato nella directory fino a quando [non viene completata la verifica](active-directory-privileged-identity-management-how-to-complete-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sommario PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->