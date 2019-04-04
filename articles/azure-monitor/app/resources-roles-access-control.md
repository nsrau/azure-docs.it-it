---
title: Risorse, ruoli e controllo di accesso in Azure Application Insights | Microsoft Docs
description: Proprietari, collaboratori e lettori di informazioni dell'organizzazione.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: mbullwin
ms.openlocfilehash: 213f4313e96638e4d94455be5f16aa3221d35b73
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905682"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Risorse, ruoli e controllo di accesso in Application Insights

È possibile controllare chi ha eseguito la lettura e aggiornare l'accesso ai dati in Azure [Application Insights][start] mediante il [controllo degli accessi in base al ruolo in Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Assegnare l'accesso agli utenti nella **sottoscrizione o nel gruppo di risorse** a cui la risorsa dell'applicazione appartiene, non nella risorsa stessa. Assegnare il ruolo **collaboratore componente di Application Insights** . In tal modo si garantisce un controllo di accesso uniforme ai test Web e agli avvisi nonché alla risorsa dell'applicazione. [Altre informazioni](#access)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Risorse, gruppi e sottoscrizioni

Innanzitutto prendere nota di alcune definizioni:

* **Risorse** : un'istanza di un servizio di Microsoft Azure. La risorsa di Application Insights raccoglie, analizza e visualizza i dati di telemetria inviati dall'applicazione.  Altri tipi di risorse di Azure includono app Web, database e macchine virtuali.
  
    Per visualizzare le risorse, aprire il [portale di Azure][portal], eseguire l'accesso e fare clic su Tutte le risorse. Per trovare una risorsa, digitare parte del nome nel campo del filtro.
  
    ![Elenco di risorse di Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Gruppo di risorse**][group]: ogni risorsa appartiene a un gruppo. Un gruppo è un modo pratico per gestire risorse correlate, in particolare per il controllo di accesso. In un gruppo di risorse, ad esempio, è possibile inserire un'app Web, una risorsa di Application Insights per monitorare l'app e una risorsa di archiviazione per conservare i dati esportati.

* [**Sottoscrizione**](https://portal.azure.com): per usare Application Insights o altre risorse di Azure, si accede a una sottoscrizione di Azure. Ogni gruppo di risorse appartiene a una sottoscrizione di Azure, dove si sceglie il pacchetto di prezzo e, se è la sottoscrizione di un'organizzazione, si scelgono i membri e le relative autorizzazioni di accesso.
* [**Account Microsoft**][account]: il nome utente e la password usati per accedere alle sottoscrizioni di Microsoft Azure, XBox Live, Outlook.com e altri servizi Microsoft.

## <a name="access"></a> Controllare l'accesso nel gruppo di risorse

È importante comprendere che oltre la risorsa creata per l'applicazione, sono disponibili anche risorse nascoste distinte per avvisi e i test Web. Vengono collegati allo stesso [gruppo di risorse](#resource-group) dell'applicazione. È anche possibile che siano stati inseriti altri servizi di Azure in tale posizione, ad esempio siti Web o risorsa di archiviazione.

Per controllare l'accesso a queste risorse, è quindi consigliabile:

* Controllare l'accesso al livello della **sottoscrizione o del gruppo di risorse** .
* Assegnare il ruolo **collaboratore componente di Application Insights** agli utenti. In tal modo si consente loro di modificare i test Web, gli avvisi e le risorse di Application Insights, senza fornire l'accesso a tutti gli altri servizi del gruppo.

## <a name="to-provide-access-to-another-user"></a>Per fornire l'accesso a un altro utente

È necessario disporre dei diritti di proprietario per la sottoscrizione o per il gruppo di risorse.

L'utente deve avere un [account Microsoft][account] o l'accesso all'[account Microsoft aziendale](../../active-directory/fundamentals/sign-up-organization.md). È possibile fornire l'accesso a utenti e anche a gruppi di utenti definiti in Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Passare al gruppo di risorse o direttamente alla risorsa stessa

Selezionare **Controllo di accesso (IAM)** dal menu a sinistra.

![Screenshot del pulsante del controllo di accesso nel portale di Azure](./media/resources-roles-access-control/0001-access-control.png)

Selezionare **Aggiungi assegnazione di ruolo**

![Screenshot del menu di controllo di accesso con il pulsante Aggiungi evidenziato in rosso](./media/resources-roles-access-control/0002-add.png)

La vista **Aggiungi autorizzazioni** seguente riguarda principalmente le risorse di Application Insights. Se si stanno visualizzando le autorizzazioni di controllo di accesso da un livello superiore, ad esempio i gruppi di risorse, si vedrebbero ruoli aggiuntivi non incentrati su Application Insights.

Per visualizzare informazioni su tutti i ruoli predefiniti di controllo degli accessi in base al ruolo di Azure usare il [contenuto di riferimento ufficiale](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Screenshot dell'elenco dei ruoli utente di controllo di accesso](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Selezionare un ruolo

Dove applicabile sarà presente un collegamento alla documentazione di riferimento ufficiale associata.

| Ruolo | Nel gruppo di risorse |
| --- | --- |
| [Proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Può apportare qualsiasi modifica, incluso l'accesso utente. |
| [Collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Può apportare qualsiasi modifica, incluse tutte le risorse. |
| [collaboratore componente di Application Insights ](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Può modificare risorse, test Web e avvisi di Application Insights. |
| [Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Può visualizzare qualsiasi oggetto ma non può apportare alcuna modifica. |
| [Debugger di snapshot di Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Concede l'autorizzazione utente per l'uso delle funzionalità di Snapshot Debugger di Application Insights. Si noti che questo ruolo non è incluso nei ruoli Proprietario e Collaboratore. |
| Collaboratore alla gestione delle versioni di distribuzione dei servizi di Azure | Ruolo di collaboratore per i servizi di distribuzione tramite la distribuzione dei servizi di Azure. |
| [Pulizia dati](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Ruolo speciale per l'eliminazione dei dati personali. Vedere le [linee guida per i dati personali](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) per altre informazioni.   |
| Amministratore di ExpressRoute | Può creare, eliminare e gestire le istanze di ExpressRoute.|
| [Collaboratore di Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Il ruolo Collaboratore di Log Analytics può leggere tutti i dati di monitoraggio e modificare le impostazioni di monitoraggio. La modifica delle impostazioni di monitoraggio include l'aggiunta di estensioni delle VM alle VM, la lettura delle chiavi dell'account di archiviazione per potere configurare la raccolta di log dall'Archiviazione di Azure, la creazione e la configurazione degli account di Automazione, l'aggiunta di soluzioni e la configurazione di Diagnostica di Azure in tutte le risorse di Azure.  |
| [Lettore di Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Il ruolo Lettore di Log Analytics può visualizzare ed eseguire ricerche in tutti i dati di monitoraggio e può visualizzare le impostazioni di monitoraggio, inclusa la visualizzazione della configurazione di Diagnostica di Azure in tutte le risorse di Azure. |
| masterreader | Consente a un utente di visualizzare tutti gli elementi, ma senza apportare modifiche. |
| [Collaboratore al monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Può leggere tutti i dati di monitoraggio e aggiornare le impostazioni di monitoraggio. |
| [Autore delle metriche di monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Abilita la pubblicazione di metriche delle risorse di Azure. |
| [Lettore di monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Può leggere tutti i dati di monitoraggio. |
| Collaboratore ai criteri delle risorse (anteprima) | Utenti di cui sono state recuperate informazioni da EA, con diritti per la creazione/modifica di criteri delle risorse, la creazione di ticket di supporto e la lettura di risorse/gerarchia.  |
| [Amministratore accessi utente](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Consente a un utente gestire l'accesso per altri utenti alle risorse di Azure.|
| [Collaboratore siti Web](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Consente di gestire i siti Web (non i piani Web), ma non di accedervi.|

'Modifica' include la creazione, l'eliminazione e l'aggiornamento:

* Risorse
* Test Web
* Avvisi
* Esportazione continua

#### <a name="select-the-user"></a>Selezionare l'utente

Se l'utente desiderato non è nella directory, è possibile invitare chiunque disponga di un account Microsoft.
Se si usano servizi come Outlook.com, OneDrive, Windows Phone o XBox Live, hanno un account Microsoft.

## <a name="related-content"></a>Contenuti correlati

* [In base al ruolo controllo degli accessi in Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Query di PowerShell per determinare l'appartenenza al ruolo

Poiché alcuni ruoli possono essere collegati a notifiche e avvisi tramite posta elettronica, può essere utile essere in grado di generare un elenco di utenti appartenenti a un determinato ruolo. Per facilitare la generazione di questi tipi di elenchi sono disponibili le query di esempio seguenti, che possono essere modificate per soddisfare le specifiche esigenze:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Query dell'intera sottoscrizione per i ruoli di Amministratore e Collaboratore

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Query all'interno del contesto di una risorsa specifica di Application Insights per proprietari e collaboratori

```powershell
$resourceGroup = “RGNAME”
$resourceName = “AppInsightsName”
$resourceType = “microsoft.insights/components”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Query all'interno del contesto di un gruppo di risorse specifico per proprietari e collaboratori

```powershell
$resourceGroup = “RGNAME”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
