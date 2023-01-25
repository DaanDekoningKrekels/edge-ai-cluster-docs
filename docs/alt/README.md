# Alternatieve oplossingen

> Verschillende alternatieven die we geprobeerd hebben zoals tf serving

Er zijn ook nog alternatieve benaderingen. Er kan bij een cluster gebleven worden, maar ook gekeken worden naar andere manieren om TPU's samen te gebruiken. 

Een mogelijke oplossing om de Edge TPU's te gebruiken in een cluster is door gebruik te maken van TensorFlow Serving. Dat is een server applicatie die de TPU's toegankelijk kan maken over het netwerk. Aan de andere kant, kan er worden afgestapt van een cluster en kunnen er meerder TPU's op hetzelfde systeem worden aangesloten via een PCIe verbinding. 