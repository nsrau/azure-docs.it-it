---
title: Livello di compatibilità del modello di dati in Azure Analysis Services | Microsoft Docs
description: Informazioni sul livello di compatibilità del modello di dati tabulari.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31ca6deef6d81ca7beb08f6df1a15d52ef381a46
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190392"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Livello di compatibilità per i modelli tabulari di Analysis Services

Il termine *livello di compatibilità* fa riferimento a comportamenti specifici di ogni versione nel motore di Analysis Services. Le modifiche del livello di compatibilità coincidono in genere con il rilascio di versioni principali di SQL Server. Queste modifiche vengono implementate anche in Azure Analysis Services per mantenere allineate entrambe le piattaforme. Le modifiche del livello di compatibilità influiscono anche sulle funzionalità disponibili nei modelli tabulari. Ad esempio, DirectQuery e i metadati degli oggetti tabulari hanno implementazioni diverse a seconda del livello di compatibilità. Il livello di compatibilità è specificato nel progetto del modello tabulare in Visual Studio (SSDT). I modelli tabulari creati e importati in Power BI Desktop hanno il livello di compatibilità 1400.

Azure Analysis Services supporta i modelli tabulari ai livelli di compatibilità 1200 e 1400. 

> [!NOTE]
> La versione di Power BI Desktop del mese di settembre 2018 e le versioni successive hanno un livello di compatibilità 1465 con i file con estensione pbix. Questo livello di compatibilità è supportato in Azure Analysis Services. L'importazione di un file di Power BI Desktop non è tuttavia consigliabile per gli ambienti di produzione. Per altre informazioni, vedere [Importare un file di Power BI Desktop](analysis-services-import-pbix.md).

Il livello di compatibilità più recente è 1400. Questo livello corrisponde a SQL Server 2017 Analysis Services. Le funzionalità principali del livello di compatibilità 1400 includono:

*  Nuove funzioni per la connettività dati e l'importazione con il supporto delle API TOM e degli script TMSL. 
*  Funzionalità di trasformazione e mashup dei dati tramite Recupera dati ed espressioni M.
*  Le misure supportano una proprietà Espressione di righe di dettaglio con un'espressione DAX. Questa proprietà consente a strumenti client quali Microsoft Excel di eseguire il drill-down per visualizzare dati dettagliati da un report aggregato. Ad esempio, quando gli utenti visualizzano le vendite totali per un'area geografica e un mese, possono visualizzare i dettagli degli ordini associati. 
*  Sicurezza a livello di oggetto per i nomi di tabelle e colonne, oltre ai dati al loro interno.
*  Supporto migliorato per le gerarchie incomplete.
*  Miglioramenti per prestazioni e monitoraggio.
 
## <a name="set-compatibility-level"></a>Impostare il livello di compatibilità

 Quando si crea un nuovo progetto di modello tabulare in SSDT, è possibile specificare il livello di compatibilità nella finestra di dialogo **Progettazione modelli tabulari**. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Se si seleziona l'opzione **Non visualizzare più questo messaggio**, tutti i progetti successivi usano il livello di compatibilità specificato come predefinito. È possibile modificare il livello di compatibilità predefinito in SSDT in **Strumenti** > **Opzioni**.  
  
 Per aggiornare un progetto di modello tabulare esistente in SSDT, impostare la proprietà **Livello di compatibilità** nella finestra **Proprietà** del modello. Tenere presente che l'aggiornamento del livello di compatibilità è irreversibile.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Controllare il livello di compatibilità per un database con modello tabulare in SQL Server Management Studio 

 In SQL Server Management Studio fare clic con il pulsante destro del mouse sul nome del database e scegliere **Proprietà** > **Livello di compatibilità**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Controllare il livello di compatibilità supportato per un server in SQL Server Management Studio  

 In SQL Server Management Studio fare clic con il pulsante destro del mouse sul nome del server e scegliere **Proprietà** > **Livello di compatibilità supportato**.  
  
 Questa proprietà specifica il livello di compatibilità più alto per un database eseguito nel server (escludendo l'anteprima). Il livello di compatibilità supportato non può essere modificato.  

## <a name="next-steps"></a>Passaggi successivi

  [Creare un modello nel portale di Azure](analysis-services-create-model-portal.md)   
  [Gestire Analysis Services](analysis-services-manage.md)  
