<properties
   pageTitle="Azure Privileged Identity Management: Come avviare una revisione della sicurezza"
   description="Informazioni su come creare una revisione della sicurezza per identità con privilegi con l'estensione Azure Privileged Identity Management."
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

# Azure Privileged Identity Management: Come avviare una revisione della sicurezza

## Avvio di una revisione della sicurezza
Infine, sarà possibile eseguire revisioni della sicurezza in altre posizioni nel portale di Azure. Questo documento illustra i passaggi per l'avvio di un'analisi della sicurezza nell'interfaccia **Privileged Identity Management (PIM)**.

È possibile che vi siano utenti non riconosciuti o che cambiano incarichi o progetti e non necessitano più di un accesso con privilegi nella nuova posizione. Per ridurre il rischio associato ad assegnazioni di ruoli "non aggiornate", è possibile eseguire la revisione dei ruoli assegnati agli utenti eseguendo una revisione della sicurezza.

## Percorsi per avviare una revisione della sicurezza
> [AZURE.NOTE]Se non è stato ancora creato un dashboard PIM nel portale di Azure, vedere i passaggi descritti nell'articolo relativo all'[introduzione ad Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)

Dal dashboard di Azure PIM è possibile avviare una revisione da uno dei percorsi seguenti:

- Dashboard > Revisioni sicurezza > Revisione > pulsante Revisione
- Dashboard > Ruoli > pulsante Revisione
- Dashboard > fare clic sul ruolo da rivedere nell'elenco dei ruoli > pulsante Revisione

## Avviare una revisione della sicurezza

Quando si fa clic sul pulsante **Revisione**, vengono visualizzati i pannelli **Avvia revisione ruolo** e **Seleziona ruolo da rivedere** e risulta selezionato l'elemento **Seleziona ruolo da rivedere**.

### Selezionare il ruolo da rivedere

1. Selezionare il ruolo dall'elenco dei ruoli nel pannello **Seleziona ruolo da rivedere**. È possibile scegliere solo un ruolo per volta. Il pannello **Seleziona ruolo da rivedere** verrà sostituito dal pannello **Seleziona revisori**. Per la selezione dei revisori sono disponibili due opzioni:
  - Io: usare questa funzionalità per visualizzare un'anteprima del funzionamento delle revisioni della sicurezza senza coinvolgere altri amministratori.
  - Revisione self-service da membri del ruolo: usare questa funzionalità se si desidera che gli utenti rivedano personalmente le proprie assegnazioni di ruoli.
2. Selezionare una di queste due opzioni per iniziare a esaminare i dettagli della revisione. Verrà visualizzato il pannello **Modifica impostazioni predefinite**.

### Revisione self-service

1. Denominare la revisione immettendo il nome della revisione nel campo **Nome**. È consigliabile assegnare alla revisione un nome univoco descrittivo che consenta di tenerne facilmente traccia.
2. Immettere una data di inizio della revisione nel campo **Data inizio**.
3. Immettere una data di fine della revisione nel campo **Data fine**. Sono elencati di seguito alcuni aspetti da considerare quando si imposta la data di fine della revisione:
  - Numero di persone da sottoporre a revisione
  - Rapidità con cui gli utenti saranno in grado di aggiungere l'estensione e completare la revisione
4. Fare clic su OK nel pannello **Modifica impostazioni predefinite**. Il pannello verrà chiuso.
5. Fare clic su OK nel pannello **Avvia revisione ruolo**. Il pannello verrà chiuso. Nel menu principale del portale verrà visualizzata una notifica. Aggiornare il dashboard facendo clic sul pulsante **Aggiorna** e la revisione della sicurezza verrà visualizzata nella sezione **Revisioni sicurezza**.
6. Notificare agli utenti del ruolo che dovranno aggiungere l'estensione e quindi rivedere il proprio accesso amministrativo. Vedere il passaggio successivo.
6. [Rivedere l'accesso amministrativo](active-directory-privileged-identity-management-how-to-perform-security-review.md)

### Revisione di tipo "Io"

Se è stata selezionata l'opzione "Io" come revisore, passare alla revisione della sicurezza. Per altre informazioni su come completare la revisione, vedere [Azure Privileged Identity Management: Come eseguire una revisione della sicurezza](active-directory-privileged-identity-management-how-to-perform-security-review.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sommario PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->