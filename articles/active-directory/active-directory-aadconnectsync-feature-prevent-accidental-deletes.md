<properties
   pageTitle="Sincronizzazione di Azure AD Connect: impedire eliminazioni accidentali | Microsoft Azure"
   description="In questo argomento viene descritta la funzionalità relativa alla prevenzione delle eliminazioni accidentali (impedire eliminazioni accidentali) in AD Connect di Azure."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/27/2016"
   ms.author="andkjell"/>

# Servizio di sincronizzazione Azure AD Connect: Impedire eliminazioni accidentali
In questo argomento viene descritta la funzionalità relativa alla prevenzione delle eliminazioni accidentali (impedire eliminazioni accidentali) in AD Connect di Azure.

Durante l'installazione di Azure AD Connect verrà attivata per impostazione predefinita la funzionalità per evitare le eliminazioni accidentali e verrà configurata in modo da non consentire un'esportazione con più di 500 eliminazioni. Questa funzionalità è progettata per la protezione da modifiche accidentali della configurazione e da modifiche alla directory locale che possono riguardare un numero elevato di utenti e altri oggetti.

Gli scenari comuni in cui si verifica questa situazione comprendono:

- Modifiche ai [filtri](active-directory-aadconnectsync-configure-filtering.md) in cui è deselezionata un'intera [unità organizzativa](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) o un [dominio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering).
- Vengono eliminati tutti gli oggetti in un'unità organizzativa.
- Un'unità organizzativa viene rinominata in modo che tutti gli oggetti in essa contenuti vengano considerati al di fuori dell'ambito di sincronizzazione.

Il valore predefinito di 500 oggetti può essere modificato con PowerShell utilizzando `Enable-ADSyncExportDeletionThreshold`. È consigliabile configurare questo valore in base alle dimensioni dell'organizzazione. Poiché l'utilità di pianificazione di sincronizzazione verrà eseguita ogni 30 minuti, il valore è il numero di eliminazioni visualizzate in 30 minuti.

Se il numero di eliminazioni da esportare in Azure AD è eccessivo, l'esportazione non continuerà e si riceverà un messaggio di posta elettronica simile a quello riportato di seguito:

![Messaggio di posta elettronica per evitare eliminazioni accidentali](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Gentile (contatto tecnico), Sincronizzazione delle identità: il giorno (data) è stato rilevato che il numero di eliminazioni ha superato la soglia di eliminazione per (nome dell'organizzazione). È stato inviato un totale di (numero) oggetti per l'eliminazione in questa esecuzione di sincronizzazione delle identità. È stato quindi raggiunto o superato il valore della soglia di eliminazione configurato di (numero) oggetti. Prima di continuare, è necessario confermare di voler procedere con l'elaborazione di queste eliminazioni. Per altre informazioni sull'errore indicato in questo messaggio di posta elettronica, vedere l'articolo che illustra come evitare eliminazioni accidentali.*

È anche possibile visualizzare lo stato `stopped-deletion-threshold-exceeded` nell'interfaccia utente di **Synchronization Service Manager** per il profilo di esportazione. ![Interfaccia utente di Synchronization Service Manager per evitare eliminazioni accidentali](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se si tratta di un messaggio inatteso, ricercare la causa e intraprendere eventuali azioni correttive. Per visualizzare gli oggetti che devono essere eliminati, procedere come segue:

1. Avviare **Servizio di sincronizzazione** dal Menu start.
2. Passare alla pagina **Connettori**.
3. Selezionare il connettore con tipo **Azure Active Directory**.
4. In **Azioni** a destra, selezionare **Spazio connettore di ricerca**.
5. Nella casella popup in **Ambito** selezionare **Disconnesso poiché** e selezionare un orario passato. Fare clic su **Ricerca**. Ciò permette di visualizzare tutti gli oggetti da eliminare. Facendo clic su ogni elemento, è possibile ottenere informazioni aggiuntive sull'oggetto. È inoltre possibile fare clic su **Impostazione delle colonne** per aggiungere attributi aggiuntivi da visualizzare nella griglia.

![Spazio connettore di ricerca](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Se si desidera tutte le eliminazioni, eseguire le operazioni seguenti:

1. Per disabilitare temporaneamente la protezione e consentire l’esportazione di queste eliminazioni, eseguire il cmdlet PowerShell: `Disable-ADSyncExportDeletionThreshold` Quando vengono richieste le credenziali, fornire un account di amministratore globale di Azure Active Directory e una password. ![Credenziali](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Con il connettore Azure Active Directory ancora selezionato, selezionare l'azione **Esegui** e selezionare **Esporta**.
3. Per riabilitare la protezione, eseguire il cmdlet PowerShell: `Enable-ADSyncExportDeletionThreshold`

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->