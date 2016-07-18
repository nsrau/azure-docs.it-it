<properties
   pageTitle="Come iniziare una verifica dell'accesso | Microsoft Azure"
   description="Informazioni su come creare una verifica dell'accesso per le identità con privilegi con l'applicazione Azure Privileged Identity Management."
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
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# Come avviare una verifica dell'accesso in Azure AD Privileged Identity Management

Le assegnazioni dei ruoli diventano "obsolete" quando gli utenti hanno accessi con privilegi di cui non necessitano più. Per ridurre il rischio associato alle assegnazioni dei ruoli obsolete, gli amministratori dei ruoli con privilegi devono esaminare periodicamente i ruoli assegnati agli utenti. Questo documento illustra i passaggi per l'avvio di una verifica della sicurezza in Azure AD Privileged Identity Management (PIM).

## Avviare una verifica della sicurezza
> [AZURE.NOTE] Se l'applicazione PIM non è stata aggiunta al dashboard nel portale di Azure, vedere i passaggi descritti in [Introduzione ad Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)

Nella pagina principale dell'applicazione PIM sono disponibili tre opzioni per avviare una verifica della sicurezza:

- **Verifiche di accesso** > **Aggiungi**
- **Ruoli** > pulsante **Verifica**
- Selezionare il ruolo specifico da verificare dall'elenco dei ruoli > pulsante **Verifica**

Quando fa clic sul pulsante **Verifica**, viene visualizzato il pannello **Inizia una verifica di accesso** . In questo pannello configurare la verifica assegnando un nome e un limite temporale, scegliere un ruolo da verificare e decidere chi eseguirà la verifica.

![Schermata Inizia una verifica di accesso][1]

### Configurare la verifica

Per creare una verifica di accesso, è necessario assegnare un nome e impostare le date di inizio e di fine.

![Schermata di configurazione della verifica][2]

Definire una durata della verifica che consenta agli utenti di completare l'operazione. Se la verifica termina prima della data di fine, è sempre possibile arrestare la verifica in anticipo.

### Scegliere un ruolo da verificare

Ogni verifica è incentrata su un solo ruolo. A meno che non si abbia avviato la verifica di accesso dal pannello di un ruolo specifico, è ora necessario scegliere un ruolo.

1. Passare a **Verifica l'appartenenza ai ruoli**

    ![Schermata Verifica l'appartenenza ai ruoli][3]

2. Scegliere un ruolo dall'elenco.

### Decidere chi eseguirà la verifica

Sono disponibili due opzioni per l'esecuzione di una verifica. È possibile farlo personalmente, approvando o negando l'accesso a tutti gli utenti in un ruolo. In alternativa è possibile fare in modo che ogni utente verifichi il proprio accesso.

1. Passare a **Selezionare i revisori**

    ![Schermata Selezionare i revisori][4]

2. Scegliere una delle opzioni disponibili:
    - **Me** (Io): utile se si vuole visualizzare in anteprima come funzionano le verifiche di accesso o se si vuole eseguire una verifica per conto di utenti che non possono eseguire questa operazione.
    - **Members review themselves** (I membri verificano se stessi): usare questa opzione per fare in modo che gli utenti verifichino le proprie assegnazioni di ruoli.

### Avviare la verifica

Infine è disponibile l'opzione per richiedere agli utenti di fornire un motivo se approvano l'accesso. Se si vuole, aggiungere una descrizione della verifica e selezionare **Avvia**.

Assicurarsi di informare gli utenti che è presente una verifica di accesso in attesa per loro e mostrare [Come eseguire una verifica dell'accesso](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## Gestire la verifica di accesso

È possibile tenere traccia dello stato di avanzamento delle verifiche eseguite dai revisori nella sezione Verifiche di accesso del dashboard di Azure AD PIM. Nessun diritto di accesso verrà modificato nella directory fino al [completamento della verifica](active-directory-privileged-identity-management-how-to-complete-review.md).

Fino al termine del periodo di verifica, è possibile ricordare agli utenti di completare la verifica o arrestare la verifica in anticipo nella sezione Verifiche di accesso.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Sommario PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png

<!---HONumber=AcomDC_0706_2016-->