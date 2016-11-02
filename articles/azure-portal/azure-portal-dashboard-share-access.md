<properties
   pageTitle="Accesso al dashboard del portale di Azure | Microsoft Azure"
   description="Questo articolo illustra come condividere l'accesso a un dashboard nel portale di Azure."
   services="azure-portal"
   documentationCenter=""
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="multiple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="tomfitz"/>

# Condivisione dei dashboard di Azure

Dopo aver configurato un dashboard, è possibile pubblicarlo e condividerlo con altri utenti nell'organizzazione. Si consente ad altri utenti di accedere al proprio dashboard usando il [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) Azure. Si assegna un utente o gruppo di utenti a un ruolo e questo ruolo definisce se gli utenti possono visualizzare o modificare il dashboard pubblicato.

Tutti i dashboard pubblicati vengono implementati come risorse di Azure. Ciò significa che sono disponibili come elementi all'interno della sottoscrizione e sono contenuti in un gruppo di risorse. Dal punto di vista del controllo di accesso, i dashboard non sono diversi da altre risorse, ad esempio una macchina virtuale o un account di archiviazione.

> [AZURE.TIP] I singoli riquadri del dashboard applicano requisiti di controllo di accesso personalizzati in base alle risorse che visualizzano. Perciò è possibile progettare un dashboard condiviso più ampiamente, continuando comunque a proteggere i dati nei singoli riquadri.

## Informazioni sul controllo di accesso per i dashboard

Con il controllo degli accessi in base al ruolo è possibile assegnare utenti ai ruoli a tre diversi livelli di ambito:

- subscription
- gruppo di risorse
- resource

Le autorizzazioni assegnate vengono ereditate dalla sottoscrizione fino alla risorsa. Il dashboard pubblicato è una risorsa. È quindi possibile che esistano già utenti assegnati a ruoli per la sottoscrizione, che funzionano anche con il dashboard pubblicato.

Di seguito è fornito un esempio. Si supponga di avere una sottoscrizione di Azure e che a diversi membri del team siano stati assegnati i ruoli di **Proprietario**, **Collaboratore** o **Lettore** della sottoscrizione. Gli utenti con il ruolo di proprietario o collaboratore possono elencare, visualizzare, creare, modificare o eliminare dashboard nella sottoscrizione. Gli utenti con il ruolo di lettore possono elencare e visualizzare i dashboard, ma non modificarli o eliminarli. Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard pubblicato, ad esempio per la risoluzione di un problema, ma non pubblicarle nel server. Hanno comunque la possibilità di creare una copia privata del dashboard per se.

È tuttavia possibile assegnare autorizzazioni anche al gruppo di risorse che contiene diversi dashboard o a un singolo dashboard. Ad esempio, si potrebbe decidere che un gruppo di utenti deve avere autorizzazioni limitate per la sottoscrizione, ma un accesso più ampio per un determinato dashboard. Assegnare gli utenti a un ruolo per il dashboard.

## Pubblicare dashboard

Si supponga di che aver completato la configurazione di un dashboard che si vuole condividere con un gruppo di utenti nella sottoscrizione. I passaggi seguenti illustrano un gruppo personalizzato denominato Storage Managers, o gestori di archiviazione, ma è possibile assegnare al gruppo qualsiasi altro nome. Per informazioni sulla creazione di un gruppo di Active Directory e sull'aggiunta di utenti a tale gruppo, vedere [Gestione dei gruppi in Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

1. Nel dashboard selezionare **Condividi**.

     ![Selezionare Condividi](./media/azure-portal-dashboard-share-access/select-share.png)

2. Prima di assegnare l'accesso, è necessario pubblicare il dashboard. Per impostazione predefinita, il dashboard verrà pubblicato in un gruppo di risorse denominato **dashboard**. Selezionare **Pubblica**.

     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Il dashboard viene pubblicato. Se le autorizzazioni ereditate dalla sottoscrizione sono appropriate, non è necessario eseguire altre operazioni. Altri utenti nell'organizzazione potranno accedere e modificare il dashboard in base al proprio ruolo a livello di sottoscrizione. Tuttavia, per questa esercitazione è possibile assegnare un gruppo di utenti a un ruolo per quel dashboard.

## Assegnare l'accesso a un dashboard

1. Dopo aver pubblicato il dashboard, selezionare **Gestisci utenti**.

     ![gestione utenti](./media/azure-portal-dashboard-share-access/manage-users.png)

2. Verrà visualizzato un elenco di utenti esistenti già stati assegnati a un ruolo per questo dashboard. L'elenco di utenti esistenti è diverso da quello nell'immagine seguente. È probabile che le assegnazioni vengono ereditate dalla sottoscrizione. Per aggiungere un nuovo utente o gruppo, selezionare **Aggiungi**.

     ![Aggiungi utente](./media/azure-portal-dashboard-share-access/existing-users.png)

3. Selezionare il ruolo che rappresenta le autorizzazioni da concedere. Per questo esempio selezionare **Collaboratore**.

     ![selezionare il ruolo](./media/azure-portal-dashboard-share-access/select-role.png)

4. Selezionare l'utente o il gruppo che si vuole assegnare al ruolo. Se l'utente o il gruppo desiderato non è visualizzato nell'elenco, usare la casella di ricerca. L'elenco dei gruppi disponibili dipenderà dai gruppi creati in Active Directory.

     ![Seleziona utente](./media/azure-portal-dashboard-share-access/select-user.png)

5. Al termine dell'operazione di aggiunta di utenti o gruppi, scegliere **OK**.

6. La nuova assegnazione viene aggiunta all'elenco di utenti. Si noti che il relativo **Accesso** è elencato come **Assegnato** invece di **Ereditato**.

     ![Ruoli assegnati](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## Passaggi successivi

- Per un elenco di ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md).
- Per altre informazioni sulla gestione delle risorse, vedere [Gestire le risorse di Azure mediante il portale](resource-group-portal.md).

<!---HONumber=AcomDC_0803_2016-->