## Scenario

Per illustrare meglio come creare un NSG, in questo documento verrà utilizzato lo scenario seguente.

![Scenario di una rete virtuale](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

In questo scenario si creerà un NSG per ogni subnet nella rete virtuale **TestVNet**, come illustrato di seguito:

- **NSG-FrontEnd**. Il front-end NSG verrà applicato per il subnet *front-end*, e contiene due regole:	
	- **regola-rdp**. Questa regola consente il traffico RDP verso il subnet *front-end*.
	- **regola-web**. Questa regola consente il traffico HTTP verso il subnet *front-end*.
- **Back-end di NSG**. Il back-end NSG verrà applicato per il subnet *back-end*, e contiene due regole:	
	- **regola sql**. Questa regola consente il traffico SQL solo dal subnet *front-end*.
	- **regola-web**. Questa regola nega tutto il traffico associato ad internet proveniente dal subnet *back-end*.

La combinazione di queste regole crea uno scenario simile alla rete perimetrale, dove la subnet di back-end può solo ricevere traffico in ingresso per il traffico SQL dalla subnet front-end e non dispone dell'accesso a Internet, mentre la subnet front-end può comunicare con Internet e ricevere solo le richieste HTTP in ingresso.
 

<!---HONumber=Sept15_HO4-->