O Pandas tem uma função retorna faixas de tempo chamada `date_range()'.

Na tabela abaixo lista os tipos de periodos que são possíveis gerar com a função.

| Alias | Description |
+-------+-------------+
| B | Business day frequency  |
| C | Custom business day frequency (experimental) |
| D | Calendar day frequencyAlias Description |
| W | Weekly frequency |
| M | Month end frequency |
| BM | Business month end frequency |
| MS | Month start frequency |
| BMS | Business month start frequency |
| Q | Quarter end frequency |
| BQ | Business quarter end frequency |
| QS | Quarter start frequency |
| BQS | Business quarter start frequency |
| A | Year end frequency |
| BA | Business year end frequency |
| AS | Year start frequency |
| BAS | Business year start frequency |
| H | Hourly frequency |
| T | Minutely frequency |
| S | Secondly frequency |
| L | Milliseconds |
| U | Microseconds |

Os seguintes parametros são possíveis usar para ajustar a faixa de tempo gerada:

| Parameter | Format | Description |
+-----------+--------+-------------+
| start | string/datetime | Left bound for generating dates |
| end | string/datetime | Right bound for generating dates |
| periods | integer/None | Number of periods (if start or end is None)Parameter Format
Description |
| freq | string/Date | OffsetFrequency string, e.g., 5D for 5 days |
| tz | string/None | Time zone name for localized index |
| normalize |bool, default None | Normalizes start and end to midnight |
| name | string, default None | Name of resulting index

Esta faixa de tempo, é idealizada para uso como Index no DataFrame.
