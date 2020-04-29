---
title: includere il file
description: File di inclusione
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335114"
---
| Risorsa | Limite |
| --- | --- |
| Gruppi di gestione per directory | 10,000 |
| Sottoscrizioni per gruppo di gestione | Senza limiti. |
| Livelli della gerarchia dei gruppi di gestione | Livello radice più 6 livelli<sup>1</sup> |
| Gruppo di gestione diretto padre per gruppo di gestione | Uno |
| [Distribuzioni a livello di gruppo di gestione](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per ogni località | 800<sup>2</sup> |

<sup>1</sup> I 6 livelli non includono il livello di sottoscrizione.

<sup>2</sup> Se si raggiunge il limite di 800 distribuzioni, eliminare le distribuzioni dalla cronologia che non sono più necessarie. Per eliminare le distribuzioni a livello di gruppo di gestione, usare [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) o [AZ Deployment mg Delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
