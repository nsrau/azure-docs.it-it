## <a name="service-chaining---transit-through-peered-vnet"></a>Concatenamento dei servizi: passaggio attraverso la rete virtuale con peering
Sebbene l'utilizzo di route del sistema faciliti il traffico automaticamente per la distribuzione, esistono casi in cui si desidera controllare il routing dei pacchetti tramite un dispositivo virtuale.
In questo scenario esistono due reti virtuali in una sottoscrizione, HubVNet e VNet1, come descritto nel diagramma di seguito. Si distribuisce un'appliance virtuale di rete nella rete virtuale HubVNet. Dopo avere stabilito il peering reti virtuali tra HubVNet e VNet1, è possibile configurare route definite dall'utente e specificare l'hop successivo all'appliance virtuale di rete in HubVNet.

![Passaggio attraverso un'appliance virtuale di rete](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [!NOTE]
> Per semplicità, si supponga che tutte le reti virtuali descritte si trovino nella stessa sottoscrizione. Questo approccio funziona tuttavia anche per uno scenario con collegamenti tra sottoscrizioni.
> 
> 

La proprietà principale per abilitare il routing di transito è il parametro "Consentire traffico inoltrato". In questo modo è possibile accettare e inviare il traffico da e verso l'appliance virtuale di rete nella rete virtuale con peering.  

