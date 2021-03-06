---
title: File di inclusione
description: File di inclusione
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012076"
---
## <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si crea una rete virtuale e una subnet.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **Crea nuovo**, immettere **\<resource-group-name>** , quindi selezionare OK oppure selezionare un valore **\<resource-group-name>** esistente in base ai parametri. |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **\<virtual-network-name>**                                    |
    | Region           | Selezionare **\<region-name>** |

3. Selezionare la scheda **Indirizzi IP** oppure il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

4. Nella scheda **Indirizzi IP** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Immettere **\<IPv4-address-space>** |

5. In **Nome subnet** selezionare la parola **predefinito**.

6. In **Modifica subnet** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Immettere **\<subnet-name>** |
    | Intervallo di indirizzi subnet | Immettere **\<subnet-address-range>**

7. Selezionare **Salva**.

8. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

9. Selezionare **Create** (Crea).