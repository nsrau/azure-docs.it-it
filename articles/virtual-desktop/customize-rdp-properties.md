---
title: Personalizzare le proprietà RDP con PowerShell-Azure
description: Come personalizzare le proprietà RDP per desktop virtuale Windows con i cmdlet di PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d41df8fe4f411cf28af83c8b4b667f1e86bdb45d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259921"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizzare le proprietà Remote Desktop Protocol per un pool host

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La personalizzazione delle proprietà di Remote Desktop Protocol (RDP) di un pool host, ad esempio l'esperienza a più monitor e il reindirizzamento audio, ti permette di offrire un'esperienza ottimale agli utenti in base alle esigenze. È possibile personalizzare le proprietà RDP nel desktop virtuale di Windows usando il portale di Azure o usando il parametro *-CustomRdpProperty* nel cmdlet **Update-AzWvdHostPool** .

Per un elenco completo delle proprietà supportate e dei relativi valori predefiniti, vedere [impostazioni del file RDP supportate](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, seguire le istruzioni riportate in [Configurare il modulo PowerShell di Desktop virtuale Windows](powershell-module.md) per configurare il modulo PowerShell e accedere ad Azure.

## <a name="default-rdp-properties"></a>Proprietà RDP predefinite

Per impostazione predefinita, i file RDP pubblicati contengono le proprietà seguenti:

|Proprietà di RDP | Desktop | RemoteApps |
|---|---| --- |
| Modalità multimonitor | Abilitato | N/A |
| Reindirizzamento unità abilitati | Unità, appunti, stampanti, porte COM, dispositivi USB e smart card| Unità, appunti e stampanti |
| Modalità audio remoto | Riproduci localmente | Riproduci localmente |

Le proprietà personalizzate definite per il pool host sostituiranno queste impostazioni predefinite.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Configurare le proprietà RDP nel portale di Azure

Per configurare le proprietà RDP nel portale di Azure:

1. Accedere ad Azure all'indirizzo <https://portal.azure.com>.
2. Immettere **desktop virtuale Windows** nella barra di ricerca.
3. In servizi selezionare **desktop virtuale di Windows**.
4. Nella pagina desktop virtuale di Windows selezionare **pool host** nel menu sul lato sinistro della schermata.
5. Selezionare **il nome del pool host** che si desidera aggiornare.
6. Selezionare **Proprietà** nel menu sul lato sinistro della schermata.
7. Nella scheda **Proprietà** passare a **Impostazioni RDP** per iniziare a modificare le proprietà RDP. Le proprietà devono essere in un formato separato da punti e virgola come gli esempi di PowerShell.
8. Al termine, selezionare **Salva** per salvare le modifiche.

Nelle sezioni successive verrà illustrato come modificare manualmente le proprietà RDP personalizzate in PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Aggiungere o modificare una singola proprietà RDP personalizzata

Per aggiungere o modificare una singola proprietà RDP personalizzata, eseguire il cmdlet PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Per verificare se il cmdlet appena eseguito ha aggiornato la proprietà, eseguire questo cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Se ad esempio è stata verificata la presenza della proprietà "audiocapturemode" in un pool host denominato 0301HP, è necessario immettere il cmdlet seguente:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Aggiungere o modificare più proprietà RDP personalizzate

Per aggiungere o modificare più proprietà RDP personalizzate, eseguire i cmdlet di PowerShell seguenti fornendo le proprietà RDP personalizzate come stringa con valori delimitati da punto e virgola:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

È possibile verificare che la proprietà RDP sia stata aggiunta eseguendo il cmdlet seguente:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

In base all'esempio di cmdlet precedente, se si configurano più proprietà RDP nel pool di host 0301HP, il cmdlet avrà un aspetto simile al seguente:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Reimposta tutte le proprietà RDP personalizzate

È possibile reimpostare i valori predefiniti per le singole proprietà RDP personalizzate seguendo le istruzioni in [aggiungere o modificare una singola proprietà RDP](#add-or-edit-a-single-custom-rdp-property)personalizzata oppure è possibile reimpostare tutte le proprietà RDP personalizzate per un pool host eseguendo il cmdlet di PowerShell seguente:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Per assicurarsi che l'impostazione sia stata rimossa correttamente, immettere il cmdlet seguente:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state personalizzate le proprietà RDP per un determinato pool di host, è possibile accedere a un client desktop virtuale di Windows per testarle come parte di una sessione utente. Queste guide dettagliate illustrano come connettersi a una sessione usando il client di propria scelta:

- [Connettersi con il client desktop di Windows](connect-windows-7-10.md)
- [Connettersi con il client Web](connect-web.md)
- [Connettersi con il client Android](connect-android.md)
- [Connettersi con il client macOS](connect-macos.md)
- [Connettersi con il client iOS](connect-ios.md)
