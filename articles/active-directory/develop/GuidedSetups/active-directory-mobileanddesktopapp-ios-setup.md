---
title: 'Introduzione a iOS con Azure AD v2: configurazione | Microsoft Docs'
description: "Informazioni sulle modalità per le applicazioni iOS (Swift) per chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 78c6ca83a2711ad81b7819a8723869980a1925a3
ms.contentlocale: it-it

---
## <a name="setting-up-your-ios-application"></a>Configurazione dell'applicazione iOS

Questa sezione fornisce istruzioni dettagliate su come creare un nuovo progetto per illustrare come integrare un'applicazione iOS (Swift) con *Accedi con Microsoft* per poter eseguire query su API Web che richiedono un token.

> Se invece si preferisce scaricare questo progetto XCode di esempio, [scaricare un progetto](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) e passare direttamente al [passaggio di configurazione](#create-an-application-express) per configurare il codice di esempio prima di eseguirlo.


## <a name="install-carthage-to-download-and-build-msal"></a>Installare Carthage per scaricare e compilare MSAL
L'utilità di gestione pacchetti di Carthage viene usata durante il periodo di anteprima di MSAL: esegue l'integrazione con XCode lasciando a Microsoft la possibilità di apportare modifiche alla libreria.

- Scaricare e installare la versione più recente di Carthage [qui](https://github.com/Carthage/Carthage/releases "URL di download di Carthage")

## <a name="creating-your-application"></a>Creazione dell'applicazione

1.  Aprire Xcode e selezionare `Create a new Xcode project`
2.  Selezionare `iOS` > `Single view Application` e fare clic su *Next* (Avanti)
3.  Assegnare un nome prodotto e fare clic su *Next* (Avanti)
4.  Selezionare una cartella per creare l'app e fare clic su *Create* (Crea)

## <a name="build-the-msal-framework"></a>Creare il framework MSAL

Seguire queste istruzioni per eseguire il pull e quindi creare la versione più recente delle librerie MSAL usando Carthage:

1.  Aprire il terminale Bash e andare alla cartella radice dell'app
2.  Copiare il codice seguente e incollarlo nel terminale Bash per creare un file "Cartfile":

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Copiare e incollare il codice seguente. Questo comando recupera le dipendenze in una cartella Carthage/Checkouts, quindi crea la libreria MSAL:
</li>
</ol>

```bash
carthage update
```

> Il processo precedente viene usato per scaricare e creare l'istanza di Microsoft Authentication Library (MSAL) che gestisce l'acquisizione, la memorizzazione nella cache e l'aggiornamento dei token utente usati per accedere ad API protette da Azure Active Directory v2.

## <a name="add-the-msal-framework-to-your-application"></a>Aggiungere il framework MSAL all'applicazione
1.  In Xcode aprire la scheda `General`
2.  Andare alla sezione `Linked Frameworks and Libraries` e fare clic su `+`
3.  Selezionare `Add other…`.
4.  Selezionare: `Carthage` > `Build` > `iOS` > `MSAL.framework` e fare clic su *Open* (Apri). `MSAL.framework` risulterà aggiunto all'elenco.
5.  Andare alla scheda `Build Phases`, fare clic sull'icona `+` e scegliere `New Run Script Phase`
6.  Aggiungere il contenuto seguente all'*area dello script*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Aggiungere il contenuto seguente a <code>Input Files</code> facendo clic su <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Creazione dell'interfaccia utente dell'applicazione
Nell'ambito del modello di progetto viene automaticamente creato un file Main.storyboard. Seguire queste istruzioni per creare l'interfaccia utente dell'app:

1.  Tenendo premuto CTRL, fare clic su `Main.storyboard` per visualizzare il menu di scelta rapida e quindi fare clic su: `Open As` > `Source Code`
2.  Sostituire il nodo `<scenes>` con il codice seguente:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign-Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```

