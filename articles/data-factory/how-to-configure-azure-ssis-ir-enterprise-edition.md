---
title: Provisioning della Enterprise Edition per il Runtime di integrazione Azure-SSIS | Microsoft Docs
description: Questo articolo descrive le funzionalità della Enterprise Edition del Runtime di integrazione Azure-SSIS e il provisioning di questa
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d2b06d044f68972ef72dd9b53401980e84ef779f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152436"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Provisioning della Enterprise Edition per il Runtime di integrazione Azure-SSIS

Il runtime di integrazione Azure-SSIS Enterprise Edition consente di usare le funzionalità avanzate e Premium seguenti:
-   Componenti Change Data Capture (CDC)
-   Connettori Oracle, Teradata e SAP BW
-   Trasformazioni e connettori di SQL Server Analysis Services (SSAS) e Azure Analysis Services (AAS)
-   Trasformazioni Raggruppamento fuzzy e Ricerca fuzzy
-   Trasformazioni Estrazione termini e Ricerca termini

Alcune di queste funzionalità richiedono l'installazione di componenti aggiuntivi per personalizzare il runtime di integrazione Azure-SSIS. Per altre informazioni su come installare componenti aggiuntivi, vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Funzionalità Enterprise

| **Funzionalità Enterprise** | **Descrizioni** |
|---|---|
| Componenti CDC | L'origine CDC, l'attività di controllo e la trasformazione CDC Splitter sono preinstallate nel runtime di integrazione Azure-SSIS Enterprise Edition. Per connettersi a Oracle, è necessario anche installare CDC Designer e CDC Service in un altro computer. |
| Connettori Oracle | La gestione connessione, l'origine e la destinazione Oracle sono preinstallate nel runtime di integrazione Azure-SSIS Enterprise Edition. Occorre inoltre installare il driver OCI (Oracle Call Interface) e, se necessario, configurare il servizio TNS (Transport Network Substrate) Oracle nel runtime di integrazione Azure-SSIS. Per altre informazioni, vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Connettori Teradata | È necessario installare nel runtime di integrazione Azure-SSIS Enterprise Edition la gestione connessione, l'origine e la destinazione Teradata, nonché l'API TPT (Teradata Parallel Transporter) e il driver ODBC di Teradata. Per altre informazioni, vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Connettori SAP BW | La gestione connessione, l'origine e la destinazione SAP BW sono preinstallate nel runtime di integrazione Azure-SSIS Enterprise Edition. È inoltre necessario installare il driver SAP BW nel runtime di integrazione Azure-SSIS. Questi connettori supportano SAP BW 7.0 o versioni precedenti. Per eseguire la connessione a versioni successive di SAP BW o ad altri prodotti SAP, è possibile acquistare connettori SAP da ISV di terze parti e installarli nel runtime di integrazione Azure-SSIS. Per altre informazioni su come installare componenti aggiuntivi, vedere [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Componenti di Analysis Services               | La destinazione Training modello di data mining, la destinazione Elaborazione dimensione, la destinazione Elaborazione partizione e la trasformazione Query di data mining sono preinstallate nel runtime di integrazione Azure-SSIS Enterprise Edition. Tutti questi componenti supportano SQL Server Analysis Services (SSAS), ma solo la destinazione Elaborazione partizione supporta Azure Analysis Services (AAS). Per connettersi a SSAS, è anche necessario [configurare le credenziali di autenticazione di Windows in SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Oltre a questi componenti, nel runtime di integrazione Azure-SSIS Standard/Enterprise Edition sono preinstallate l'attività Esegui DDL Analysis Services, l'attività Elaborazione Analysis Services e l'attività Query di data mining. |
| Trasformazioni Raggruppamento fuzzy e Ricerca fuzzy  | Le trasformazioni Raggruppamento fuzzy e Ricerca fuzzy sono preinstallate nel runtime di integrazione Azure-SSIS Enterprise Edition. Questi componenti supportano sia SQL Server che il database SQL di Azure per l'archiviazione dei dati di riferimento. |
| Trasformazioni Estrazione termini e Ricerca termini | Le trasformazioni Estrazione termini e Ricerca termini sono preinstallate nel runtime di integrazione Azure-SSIS Enterprise Edition. Questi componenti supportano sia SQL Server che il database SQL di Azure per l'archiviazione dei dati di riferimento. |

## <a name="instructions"></a>Istruzioni

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Scaricare e installare [Azure PowerShell](/powershell/azure/install-az-ps).

2.  Quando configura o si esegue il provisioning del runtime di integrazione Azure-SSIS con PowerShell, eseguire `Set-AzDataFactoryV2IntegrationRuntime` con **Enterprise** come valore per il parametro **Edition** prima di avviare il runtime. Ecco uno script di esempio:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Passaggi successivi

-   [Installazione personalizzata per il runtime di integrazione Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Come sviluppare componenti personalizzati a pagamento o concessi in licenza per il runtime di integrazione Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
