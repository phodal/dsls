
Source: http://repositorio.pucrs.br/dspace/bitstream/10923/13813/2/A_High_Level_DSL_for_Geospatial_Visualizations_with_Multi_core_Parallelism_Support.pdf

```
visualization:  markedmap;
settings {
  latitude:field s12;
  longitude:field 11;
  marker−text:”Camera:”field 6 image(field 15);
  page−title:”Photosb y Camera Brand”;
  size:full;
}
data {
  file: ”yfcc100m_dataset_all”;
  structure {
    delimiter: tab;
    end−register: newline;
    date−format: ”YYYY−MM−DD”;
  }
  filter: field 4 is between date ”2014−01−01” and date”2014−02−01”;
  classification {
    class(”Canon”): field 6 contains”Canon”;
    class(”Sony”): field 6 contains”Sony”;
    class(”Nikon”): field 6 contains”Nikon”;
    class(”Panasonic”): field 6 contains ”Panasonic”;
    class(”Apple”): field 6 contains”Apple”;
    class(”FUJI”): field 6 contains”FUJI”;
  }
}
```