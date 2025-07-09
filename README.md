# constructing_accountability
This repo contains the scripts used to clean, analyse, and process data from the Mexican federal program Youth Building the Future. The scripts were used for the story 'El doble blindaje de Jóvenes Construyendo el Futuro' [spanish only] published by the Mexican outlet Meganoticias. 

## 🗂️Data Source & Original Structure
Data obtain from [Padrón Único de Beneficiarios del Programa Jóvenes COnstruyendo el Futuro](https://pub.bienestar.gob.mx/v2/pub/programasIntegrales/9/5928)

35 ```.xlsx``` files were downloaded:
- 2019-2020:  Annual files, each year split in to ```.xlsx``` files due to the amount of data
These files lacked information about the ```SEX, AGE, and REGISTRATION DATE ```

- 2021:       One file for the full year

- 2022-2023:  Monthly files 

Across all files the names of the beneficiaries were splited across columns ```Surname, Second Surname, Name``` and states were coded under ```CVE_ENTIDAD```

## 🧼Normalization & Standarization

### Annual files:
Manually addded the columns ```SEX, AGE, and REGISTRATION DATE ``` as empty values to match the structure of the later datasets.

### Monthly files:
Ensured consistency of the colum names across all files and were standarized:
```NUM, CVE_ENTIDAD, CVE_MUN, PRIMER APELLIDO, SEGUNDO APELLIDO, NOMBRE, SEXO, EDAD, FECHA_ALTA, IMPORTE_BENEFICIO```

---
### Name and State standarization

Columns were concatenated into a single ```NAME```column
```python
df['NOMBRE'] = df['NOMBRE'] + ' ' + df['PRIMER APELLIDO'] + ' ' + df['SEGUNDO APELLIDO']
```

The same was done with the State code using a dictionary.
```python
state_mapping = {
    '01': 'Aguascalientes', '02': 'Baja California', '03': 'Baja California Sur', '04': 'Campeche',
    '05': 'Coahuila', '06': 'Colima', '07': 'Chiapas', '08': 'Chihuahua', '09': 'Ciudad de México',
    '10': 'Durango', '11': 'Guanajuato', '12': 'Guerrero', '13': 'Hidalgo', '14': 'Jalisco',
    '15': 'México', '16': 'Michoacán', '17': 'Morelos', '18': 'Nayarit', '19': 'Nuevo León',
    '20': 'Oaxaca', '21': 'Puebla', '22': 'Querétaro', '23': 'Quintana Roo', '24': 'San Luis Potosí',
    '25': 'Sinaloa', '26': 'Sonora', '27': 'Tabasco', '28': 'Tamaulipas', '29': 'Tlaxcala',
    '30': 'Veracruz', '31': 'Yucatán', '32': 'Zacatecas'
}
```

The final column across all files were standarized as:
```NUM, CVE_ENTIDAD, CVE_MUN, PRIMER APELLIDO, SEGUNDO APELLIDO, NOMBRE, SEXO, EDAD, FECHA_ALTA, IMPORTE_BENEFICIO```

### UID Creation & De-duplication

To handle beneficiaries and identifying the as unique, a ``UID`` column was created based on State and Municipality location along with the name of each one:
``UID = NOMBRE + "_" + CVE_ENTIDAD + "_" + CVE_MUNICIPIO``

## 📜Scripts

## 
