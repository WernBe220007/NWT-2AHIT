# NWT 2ahit

ergänzung und mitschrift zum bestehenden pdf

ipv4 adresse

besteht aus vier 0ktetten von nuller und einser

dezimaldarstellung von 0 bis 255

Klasse A: Klasse B: Klasse C:

| klasse | adressbereich               | netze   | adressen |
| ------ | --------------------------- | ------- | -------- |
| A      | 0.0.0.0 - 127.255.255.255   | 128     | 16777216 |
| B      | 128.0.0.0 - 191.255.255.255 | 16384   | 65536    |
| C      | 192.0.0.0 - 223.255.255.255 | 2097152 | 256      |

klasse D und E 

selten verwendet, multicast adressen

| Klasse | adressbereich               | multicast adressen |
| ------ | --------------------------- | ------------------ |
| D      | 224.0.0.0 - 224.0.0.255     | well known         |
| D      | 224.0.1.0 - 238.255.255.244 | globally-scoped    |
| D      | 239.0.0.0 - 293.255.255.255 | local              |

### netzmasken

netzmasken für standartklassen

| klasse A | 255.0.0.0     |
| -------- | ------------- |
| klasse B | 255.255.0.0   |
| klasse C | 255.255.255.0 |
