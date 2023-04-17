

# Création et entrainement d'un modèle

```SQL
CREATE MODEL mindsdb.votsv2_model_tcp_30pc

FROM files

  (SELECT * FROM botsv2_tcp_test_sample_30pc)

PREDICT attack;
```

# Prédictions
```sql
SELECT m.attack as predicted_attack,

d.attack as real_attack

FROM mindsdb.votsv2_model_tcp_30pc AS m

JOIN files.botsv2_tcp_etalon_20pc AS d;
```

