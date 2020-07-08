---
title: Elencare le assegnazioni di ruolo di Azure usando la portale di Azure-RBAC
description: Informazioni su come determinare le risorse a cui utenti, gruppi, entità servizio o identità gestite possono accedere usando il portale di Azure e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f7d56ecc7fc6bd850fced33c2c1cf20902bb2df4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361848"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Elencare le assegnazioni di ruolo di Azure usando il portale di Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Questo articolo descrive come elencare le assegnazioni di ruolo usando il portale di Azure.

> [!NOTE]
> Se l'organizzazione dispone di funzioni di gestione esternalizzate a un provider di servizi che usa la [gestione delle risorse delegate di Azure](../lighthouse/concepts/azure-delegated-resource-management.md), le assegnazioni di ruolo autorizzate dal provider di servizi non verranno visualizzate qui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Elencare le assegnazioni di ruolo per un utente o un gruppo

Il modo più semplice per visualizzare i ruoli assegnati a un utente o a un gruppo in una sottoscrizione consiste nell'usare il riquadro **assegnazioni di ruolo di Azure** .

1. Nella portale di Azure selezionare tutti i **Servizi** dal menu portale di Azure.

1. Selezionare **Azure Active Directory** , quindi selezionare **utenti** o **gruppi**.

1. Fare clic sull'utente o sul gruppo per il quale si desidera elencare le assegnazioni di ruolo.

1. Fare clic su **assegnazioni di ruolo di Azure**.

    Viene visualizzato un elenco di ruoli assegnati all'utente o al gruppo selezionato in diversi ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo per le quali si dispone dell'autorizzazione di lettura.

    ![Assegnazione dei ruoli a un utente](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Per modificare la sottoscrizione, fare clic sull'elenco **sottoscrizioni** .

## <a name="list-owners-of-a-subscription"></a>Elenca i proprietari di una sottoscrizione

Gli utenti a cui è stato assegnato il ruolo di [proprietario](built-in-roles.md#owner) per una sottoscrizione possono gestire tutti gli elementi nella sottoscrizione. Per elencare i proprietari di una sottoscrizione, seguire questa procedura.

1. Nel portale di Azure fare clic su **Tutti i servizi** e quindi **Sottoscrizioni**.

1. Fare clic sulla sottoscrizione di cui si desidera elencare i proprietari.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questa sottoscrizione.

1. Scorrere fino alla sezione **owners (proprietari** ) per visualizzare tutti gli utenti a cui è stato assegnato il ruolo di proprietario per questa sottoscrizione.

   ![Controllo di accesso alla sottoscrizione-scheda assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Elencare le assegnazioni di ruolo in un ambito

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

   ![Controllo di accesso - Scheda Assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Nella scheda Assegnazioni di ruolo è possibile visualizzare chi ha accesso a questo ambito. Si noterà che l'ambito di alcuni ruoli è **Questa risorsa**, mentre quello di altri è **(Ereditato)** da un altro ambito. L'accesso viene assegnato in modo specifico alla risorsa oppure ereditato da un'assegnazione nell'ambito padre.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Elencare le assegnazioni di ruolo per un utente in un ambito

Per elencare l'accesso per un utente, un gruppo, un'entità servizio o un'identità gestita, è possibile elencare le assegnazioni di ruolo. Attenersi alla procedura seguente per elencare le assegnazioni di ruolo per un singolo utente, gruppo, entità servizio o identità gestita in un determinato ambito.

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic sulla scheda **Verifica l'accesso**.

    ![Controllo di accesso - Scheda Verifica l'accesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Nell'elenco **Trova**, selezionare il tipo di entità di sicurezza di cui si intende verificare l'accesso.

1. Nella casella di ricerca, immettere una stringa per eseguire ricerche nella directory in base ai nomi visualizzati, agli indirizzi di posta elettronica o agli identificatori di oggetto.

    ![Elenco di selezione per la verifica dell'accesso](./media/role-assignments-list-portal/check-access-select.png)

1. Fare clic sull'entità di sicurezza per aprire il riquadro **Assegnazioni**.

    ![Riquadro Assegnazioni](./media/role-assignments-list-portal/check-access-assignments.png)

    In questo riquadro è possibile visualizzare i ruoli assegnati all'entità di sicurezza e all'ambito selezionati. Se sono presenti assegnazioni negate in questo ambito o ereditate da questo ambito, verranno elencate.

## <a name="list-role-assignments-for-a-managed-identity"></a>Elencare le assegnazioni di ruolo per un'identità gestita

È possibile elencare le assegnazioni di ruolo per le identità gestite assegnate dal sistema e assegnate dall'utente in un determinato ambito usando il **Pannello controllo di accesso (IAM)** come descritto in precedenza. In questa sezione viene descritto come elencare le assegnazioni di ruolo solo per l'identità gestita.

### <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

1. Nella portale di Azure aprire un'identità gestita assegnata dal sistema.

1. Nel menu a sinistra fare clic su **Identity**.

    ![Identità gestita assegnata dal sistema](./media/shared/identity-system-assigned.png)

1. In **autorizzazioni**fare clic su **assegnazioni di ruolo di Azure**.

    Viene visualizzato un elenco di ruoli assegnati all'identità gestita assegnata dal sistema selezionata in diversi ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo per le quali si dispone dell'autorizzazione di lettura.

    ![Assegnazioni di ruolo per un'identità gestita assegnata dal sistema](./media/shared/role-assignments-system-assigned.png)

1. Per modificare la sottoscrizione, fare clic sull'elenco **sottoscrizione** .

### <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

1. Nella portale di Azure aprire un'identità gestita assegnata dall'utente.

1. Fare clic su **assegnazioni di ruolo di Azure**.

    Viene visualizzato un elenco di ruoli assegnati all'identità gestita assegnata dall'utente selezionata in diversi ambiti, ad esempio gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa. Questo elenco include tutte le assegnazioni di ruolo per le quali si dispone dell'autorizzazione di lettura.

    ![Assegnazioni di ruolo per un'identità gestita assegnata dal sistema](./media/shared/role-assignments-user-assigned.png)

1. Per modificare la sottoscrizione, fare clic sull'elenco **sottoscrizione** .

## <a name="list-number-of-role-assignments"></a>Elencare il numero di assegnazioni di ruolo

È possibile avere fino a **2000** assegnazioni di ruolo in ogni sottoscrizione. Questo limite include le assegnazioni di ruolo negli ambiti di sottoscrizione, gruppo di risorse e risorsa. Per tenere traccia di questo limite, nella scheda **assegnazioni di ruolo** è incluso un grafico che elenca il numero di assegnazioni di ruolo per la sottoscrizione corrente.

![Controllo di accesso-numero di assegnazioni di ruolo](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se ci si avvicina al numero massimo e si tenta di aggiungere altre assegnazioni di ruolo, verrà visualizzato un avviso nel riquadro **Aggiungi assegnazione ruolo** . Per i modi in cui è possibile ridurre il numero di assegnazioni di ruolo, vedere [risolvere i problemi relativi a RBAC di Azure](troubleshooting.md#azure-role-assignments-limit).

![Controllo di accesso-avviso di aggiunta di assegnazione di ruolo](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments-preview"></a>Scarica assegnazioni di ruolo (anteprima)

È possibile scaricare le assegnazioni di ruolo in un ambito in formato CSV o JSON. Questa operazione può essere utile se è necessario controllare l'elenco in un foglio di calcolo o eseguire un inventario quando si esegue la migrazione di una sottoscrizione.

> [!IMPORTANT]
> Scaricare le assegnazioni di ruolo è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando si scaricano le assegnazioni di ruolo, è necessario tenere presenti i criteri seguenti:

- Se non si dispone delle autorizzazioni per leggere la directory, ad esempio il ruolo Readers directory, le colonne DisplayName, SignInName e ObjectType saranno vuote.
- Le assegnazioni di ruolo la cui entità di sicurezza è stata eliminata non sono incluse.
- L'accesso concesso agli amministratori classici non è incluso.

Seguire questa procedura per scaricare le assegnazioni di ruolo in un ambito.

1. Nella portale di Azure fare clic su **tutti i servizi** e quindi selezionare l'ambito in cui si desidera scaricare le assegnazioni di ruolo. È possibile ad esempio selezionare **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una risorsa.

1. Fare clic sulla risorsa specifica.

1. Fare clic su **Controllo di accesso (IAM)** .

1. Fare clic su **Scarica assegnazioni di ruolo (anteprima)** per aprire il riquadro Scarica assegnazioni di ruolo.

    ![Controllo di accesso: scaricare le assegnazioni di ruolo](./media/role-assignments-list-portal/download-role-assignments.png)

1. Utilizzare le caselle di controllo per selezionare le assegnazioni di ruolo che si desidera includere nel file scaricato.

    - **Ereditato** : include le assegnazioni di ruolo ereditate per l'ambito corrente.
    - **Nell'ambito corrente** includere le assegnazioni di ruolo per l'ambito corrente.
    - **Elementi figlio** : includere le assegnazioni di ruolo a livelli inferiori all'ambito corrente. Questa casella di controllo è disabilitata per ambito gruppo di gestione.

1. Selezionare il formato di file, che può essere costituito da valori delimitati da virgole (CSV) o JavaScript Object Notation (JSON).

1. Specificare il nome del file.

1. Fare clic su **Avvia** per avviare il download.

    Di seguito vengono illustrati esempi dell'output per ogni formato di file.

    ![Scaricare le assegnazioni di ruolo come CSV](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Scaricare le assegnazioni di ruolo come CSV](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-portal.md)
- [Risolvere i problemi relativi a RBAC di Azure](troubleshooting.md)
