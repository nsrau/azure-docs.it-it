---
title: "Livello di compatibilità del modello di dati in Azure Analysis Services | Microsoft Docs"
description: "Informazioni sul livello di compatibilità del modello di dati tabulari."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/07/2016
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: acb69621babf0f562bfafedfe5547e16e8f2c8af
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---

# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Livello di compatibilità per i modelli tabulari di Analysis Services

Il termine *livello di compatibilità* fa riferimento a comportamenti specifici di ogni versione nel motore di Analysis Services. Le modifiche del livello di compatibilità coincidono in genere con il rilascio di versioni principali di SQL Server. Queste modifiche vengono implementate anche in Azure Analysis Services per mantenere allineate entrambe le piattaforme. Le modifiche del livello di compatibilità influiscono anche sulle funzionalità disponibili nei modelli tabulari. Ad esempio, DirectQuery e i metadati degli oggetti tabulari hanno implementazioni diverse a seconda del livello di compatibilità. 

Azure Analysis Services supporta i modelli tabulari ai livelli di compatibilità 1200 e 1400.

Il livello di compatibilità più recente è 1400 (anteprima). Questo livello corrisponde a SQL Server 2017 Analysis Services. Le funzionalità principali del livello di compatibilità 1400 includono:

*  Nuova infrastruttura per la connettività dati e per l'importazione in modelli tabulari con il supporto delle API TOM e degli script TMSL. Questa nuova funzionalità consente il supporto per origini dati aggiuntive, ad esempio l'archiviazione BLOB di Azure.
*  Funzionalità di trasformazione e mashup dei dati tramite Recupera dati ed espressioni M.
*  Le misure supportano una proprietà Espressione di righe di dettaglio con un'espressione DAX. Questa proprietà consente a strumenti client quali Microsoft Excel di eseguire il drill-down per visualizzare dati dettagliati da un report aggregato. Ad esempio, quando gli utenti visualizzano le vendite totali per un'area geografica e un mese, possono visualizzare i dettagli degli ordini associati. 
*  Sicurezza a livello di oggetto per i nomi di tabelle e colonne, oltre ai dati al loro interno.
*  Supporto migliorato per le gerarchie incomplete.
*  Miglioramenti per prestazioni e monitoraggio.



> [!IMPORTANT]
> Il livello di compatibilità 1400 è ancora in anteprima. Alcune funzionalità potrebbero non essere completamente funzionali. È sconsigliabile usare i modelli tabulari 1400 negli ambienti di produzione. 
  
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
