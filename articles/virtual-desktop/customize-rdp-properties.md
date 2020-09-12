---
title: Personalizzare le proprietà RDP con PowerShell-Azure
description: Come personalizzare le proprietà RDP per desktop virtuale Windows con i cmdlet di PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7c4bda1ecf28e964db6ba672157790114affe650
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462225"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Personalizzare le proprietà di Remote Desktop Protocol (RDP) per un pool host

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).

La personalizzazione delle proprietà di Remote Desktop Protocol (RDP) di un pool host, ad esempio l'esperienza a più monitor e il reindirizzamento audio, ti permette di offrire un'esperienza ottimale agli utenti in base alle esigenze. È possibile personalizzare le proprietà RDP nel desktop virtuale di Windows usando il portale di Azure o usando il parametro *-CustomRdpProperty* nel cmdlet **Update-AzWvdHostPool** .

Per un elenco completo delle proprietà supportate e dei relativi valori predefiniti, vedere [impostazioni del file RDP supportate](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

## <a name="default-rdp-file-properties"></a>Proprietà predefinite del file RDP

Per impostazione predefinita, i file RDP hanno le proprietà seguenti:

|RDP (proprietà)|Sul desktop|Come RemoteApp|
|---|---|---|
|Modalità multimonitor|Attivato|N/D|
|Reindirizzamento unità abilitati|Unità, appunti, stampanti, porte COM, dispositivi USB e smart card|Unità, appunti e stampanti|
|Modalità audio remoto|Riproduci localmente|Riproduci localmente|

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, seguire le istruzioni riportate in [Configurare il modulo PowerShell di Desktop virtuale Windows](powershell-module.md) per configurare il modulo PowerShell e accedere ad Azure.

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
