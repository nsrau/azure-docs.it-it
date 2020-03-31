---
title: Profilo utente e ID da usare con Azure NotebookS Preview
description: Come creare e gestire il profilo utente e l'ID utente con blocchi appunti di Azure, che diventa parte dell'URL dei blocchi appunti condivisi.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646280"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Il profilo e l'ID utente per Azure NotebookS Preview

Nello spazio potente e collaborativo di Azure Notebooks, il profilo utente presenta l'immagine pubblica agli altri:

[![Pagina del profilo Blocchi appunti di AzureAn Azure Notebooks profile page](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

L'ID utente è parte della URL usata per condividere i progetti e i notebook. L'elenco seguente descrive i diversi modelli di URL:

- `https://notebooks.azure.com/<user_id>`: Pagina del tuo profilo.
- `https://notebooks.azure.com/<user_id>/projects`: i tuoi progetti. Vengono visualizzati tutti i progetti; gli altri utenti vedono solo i progetti pubblici.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: file di progetto.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: cloni di un progetto specifico.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: l'anteprima HTML di un blocco appunti o di un file specifico.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>ID utente

Quando si accede ad Azure Notebooks per la prima volta, all'account viene assegnato automaticamente un ID utente temporaneo, per esempio "anon-idr3ca". A condizione che si disponga di un ID utente che inizia con "anon-", Azure Notebooks richiede di modificarlo ogni volta che si effettua l'accesso:

![Richiedere di creare un ID utente durante la registrazione su Azure Notebooks](media/accounts/create-user-id.png)

Accanto al nome utente temporaneo compare anche il comando **Configura ID utente**:

![Comando Configura ID utente che compare quando si usa un ID temporaneo](media/accounts/configure-user-id-command.png)

È anche possibile modificare l'ID utente in qualsiasi momento nella pagina del profilo.

Un ID utente deve essere composto da quattro a sedici lettere, numeri e trattini. Non sono consentiti altri caratteri e l'ID utente non può iniziare o terminare con un trattino né è possibile utilizzare più trattini in fila. Poiché gli ID utente sono univoci in tutti gli account di Blocco appunti di Azure, è possibile che venga visualizzato il messaggio "ID utente già in uso". (Il messaggio viene visualizzato anche se si tenta di utilizzare un marchio Microsoft come ID utente.) In questi casi, scegliere un ID utente diverso.

> [!Important]
> La modifica del proprio ID rende non valido qualsiasi URL eventualmente condiviso con l'ID precedente. È possibile modificare l'ID usando l'ID precedente per riconvalidare i collegamenti. Tuttavia, nel frattempo un altro utente può richiedere un ID inutilizzato.

## <a name="your-profile"></a>Profilo

Il profilo è composto da informazioni visualizzabili pubblicamente nella URL, `https://notebooks.azure.com/<user_id>`. La pagina del profilo mostra anche i progetti usati di recente e tutti i progetti contrassegnati con una stella.

Per modificare il profilo, usare il comando **Modifica le informazioni del profilo** nella pagina del profilo. Le sezioni del profilo sono le seguenti:

| Sezione | Sommario |
| --- | --- |
| Foto profilo | Un'immagine che viene visualizzata nella pagina del profilo. |
| Informazioni account | Il nome visualizzato, l'ID utente e l'account di posta elettronica pubblico. In questo caso l'account di posta elettronica fornisce agli altri utenti un mezzo di contatto e può essere diverso dall'[account](azure-notebooks-user-account.md) usato per accedere ad Azure Notebooks stesso. |
| Informazioni del profilo | Percorso, azienda, posizione, sito Web e una breve descrizione personale. |
| Profili social | Di GItHub, Twitter e Facebook, se li si vuole condividere. |
| Impostazioni sulla privacy | Fornisce due comandi:<ul><li>**Esporta il profilo**: crea e scarica un file *.zip* che contiene tutte le informazioni che Azure Notebooks salva nel profilo, tra cui la foto, le informazioni del profilo e i log di protezione.</li><li>**Elimina account personale:** elimina definitivamente tutte le informazioni personali archiviate nei blocchi appunti di Azure.Delete My Account : Permanently deletes all your personal information stored in Azure Notebooks.</li></ul> |
| Abilitare le funzionalità del sito | Consente di controllare gli aspetti del comportamento di Azure Notebooks:<ul><li>**Front-end unificato per Notebooks**: abilita l'avvio più veloce del notebook e una persistenza migliore.</li><li>**Esegui in JupyterLab per impostazione predefinita:** per impostazione predefinita, i blocchi appunti di Azure offrono un'interfaccia utente semplice adatta alla maggior parte degli utenti. JupyterLab fornisce un'interfaccia più completa ma più complessa per utenti esperti.</li><li>**Sito Web VNext**: consente il layout Web moderno mostrato in questa documentazione.</li></ul> |

## <a name="next-steps"></a>Passaggi successivi  

> [!div class="nextstepaction"]
> [Esercitazione: Creare ed eseguire un notebook di Jupyter per la regressione lineare](tutorial-create-run-jupyter-notebook.md)
