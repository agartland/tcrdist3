.. _workflow:

Workflow
========

Prep Adaptive Biotechnology File
--------------------------------

This data is based on data derived from Adaptive Biotechnology; however, input files contain
corrected column names (see :ref:`Inputs`) and V and J genes have been renamed to match IMGT standard nomenclature.
See more info on :ref:`loading_adaptive_biotechnology`.

Loading a TCR Dataset
---------------------

.. literalinclude:: ../tcrdist/tests/test_workflow_2.py
    :linenos:
    :lines: 6-28
    :dedent: 4
    :language: python

Distances
---------

By default, tcrdist3 calculates the distances between all TCR receptors in a repertoire 
(see a dedicated page for more details about :ref:`_tcrdistances`). The attributes 
are stored as 2D Numpy arrays which are accessible as attributes.

The weighted multi-CDR based distance is stored as the attribute `TCRrep.pw_beta`. 

.. code-block:: python

    tr.pw_beta


.. code-block::

    array([[  0., 152., 176., ..., 136., 103., 111.],
        [152.,   0., 181., ..., 141., 120., 139.],
        [176., 181.,   0., ..., 177., 180., 176.],
        ...,
        [136., 141., 177., ...,   0.,  72.,  99.],
        [103., 120., 180., ...,  72.,   0., 108.],
        [111., 139., 176., ...,  99., 108.,   0.]])

Individual components such as the distances between CDR3 are also available.
For instance, `TCRrep.pw_cdr3_b_aa`. 

.. code-block:: python

    tr.pw_cdr3_b_aa

.. code-block::

    array([[ 0, 36, 39, ..., 31, 20, 27],
       [36,  0, 40, ..., 35, 28, 33],
       [39, 40,  0, ..., 38, 39, 38],
       ...,
       [31, 35, 38, ...,  0, 24, 16],
       [20, 28, 39, ..., 24,  0, 19],
       [27, 33, 38, ..., 16, 19,  0]], dtype=int16)



Simple Clustering
-----------------

The pairwise distance matrices can be hierachically clustered. 
Each row of theh `centroids_df` DataFrame is the centroid of a cluster of TCR receptors. 

Some columns describe the cluster as a whole:

* `K_neighbors` - the number of unique clones in each cluster
* `public` - whether the cluster contains clones from multiple individuals
* `n_subjects` - the number of subject with a clone in the cluster
* `neighbors` for each clone in the cluster, the .iloc index of each clone in the tr.clone_df DataFrame.


.. literalinclude:: ../tcrdist/tests/test_workflow_2.py
    :linenos:
    :lines: 30-50
    :dedent: 4
    :language: python


+-----------+---------+-------------+------------+------------------+--------------------------------+------+-----+-----------------------------+--------------+------------+-----------+-------+----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+------------+--------+------------+------------+
| cell_type | subject | v_b_gene    | j_b_gene   | cdr3_b_aa        | epitope                        | age  | sex | cohort                      | cdr1_b_aa    | cdr2_b_aa  | pmhc_b_aa | count | clone_id | neighbors                                                                                                                                                                                                                                                                                                                                                   | K_neighbors | cluster_id | public | n_subjects | size_order |
+-----------+---------+-------------+------------+------------------+--------------------------------+------+-----+-----------------------------+--------------+------------+-----------+-------+----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+------------+--------+------------+------------+
| PBMC      | 178     | TRBV20-1*01 | TRBJ2-7*01 | CSARALEEGSYEQYF  | QYIKWPWYI,YEQYIKWPW,YEQYIKWPWY | 36.0 | M   | COVID-19-Convalescent       | DFQ......ATT | SNEG...SKA | A.SLTL    | 1     | 4        | [3, 11, 24, 40, 144, 145, 147, 152, 157, 165, 175, 177, 178, 181, 183, 184, 185, 186, 187, 188, 189, 192, 193, 194, 195, 196, 197, 198, 199, 477, 481, 514, 515, 516, 517, 519, 520, 521, 522, 523, 524, 525, 526, 527, 529, 530, 531, 532, 533, 534, 535, 536, 537, 538, 539, 541, 542, 543, 544, 545, 547, 548, 549, 550, 551, 556, 951, 952, 1149, 1564] | 70          | 14         | public | 9          | 0          |
+-----------+---------+-------------+------------+------------------+--------------------------------+------+-----+-----------------------------+--------------+------------+-----------+-------+----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+------------+--------+------------+------------+
| naive_CD8 | 10881   | TRBV19*01   | TRBJ2-1*01 | CASSLGTGNEQFF    | QYIKWPWYI,YEQYIKWPW,YEQYIKWPWY | 39.0 | F   | Healthy (No known exposure) | LNH.......DA | SQI....VND | E.KKES    | 1     | 122      | [121, 405, 410, 424, 933, 1124, 1125, 1128, 1130, 1466, 1469, 1470, 1473, 1475, 1477, 1481, 1500, 1513, 1515, 1530, 1531, 1532, 1533, 1534, 1535, 1536, 1537, 1538, 1539, 1540, 1541, 1542, 1543, 1544, 1545, 1546, 1547, 1548, 1549, 1550, 1551, 1552, 1553, 1555, 1556, 1557, 1558, 1559]                                                                 | 48          | 69         | public | 5          | 1          |
+-----------+---------+-------------+------------+------------------+--------------------------------+------+-----+-----------------------------+--------------+------------+-----------+-------+----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+------------+--------+------------+------------+
| naive_CD8 | 10881   | TRBV5-6*01  | TRBJ1-1*01 | CASSISGGTEAFF    | QYIKWPWYI,YEQYIKWPW,YEQYIKWPWY | 39.0 | F   | Healthy (No known exposure) | SGH.......DT | YYE....EEE | F.PNYS    | 1     | 274      | [273, 275, 279, 284, 285, 289, 290, 291, 292, 296, 297, 298, 301, 306, 307, 308, 309, 310, 311, 690, 695, 720, 723, 727, 728, 729, 733, 736, 759, 777, 778, 779, 781, 782, 783, 787, 1006, 1007, 1010, 1081, 1304, 1311, 1312, 1313, 1316]                                                                                                                  | 45          | 21         | public | 5          | 2          |
+-----------+---------+-------------+------------+------------------+--------------------------------+------+-----+-----------------------------+--------------+------------+-----------+-------+----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+------------+--------+------------+------------+
| PBMC      | 1349    | TRBV19*01   | TRBJ2-5*01 | CASSIWGSPQETQYF  | QYIKWPWYI,YEQYIKWPW,YEQYIKWPWY | 61.0 | M   | COVID-19-Convalescent       | LNH.......DA | SQI....VND | E.KKES    | 1     | 19       | [18, 119, 418, 419, 420, 421, 422, 423, 1126, 1127, 1129, 1418, 1419, 1421, 1467, 1482, 1496, 1497, 1498, 1499, 1501, 1502, 1503, 1504, 1505, 1506, 1507, 1508, 1509, 1510, 1511, 1512, 1516, 1517, 1518, 1519, 1520, 1521, 1522, 1523, 1524, 1525]                                                                                                         | 42          | 74         | public | 5          | 3          |
+-----------+---------+-------------+------------+------------------+--------------------------------+------+-----+-----------------------------+--------------+------------+-----------+-------+----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+------------+--------+------------+------------+
| PBMC      | 1005703 | TRBV19*01   | TRBJ1-5*01 | CASSIDLGPGNQPQHF | QYIKWPWYI,YEQYIKWPW,YEQYIKWPWY | 31.0 | F   | COVID-19-Convalescent       | LNH.......DA | SQI....VND | E.KKES    | 1     | 71       | [70, 407, 409, 1428, 1429, 1430, 1431, 1432, 1433, 1434, 1435, 1436, 1437, 1438, 1439, 1440, 1441, 1442, 1443, 1444, 1445, 1446, 1447, 1448, 1449, 1450, 1451, 1452, 1453, 1454, 1455, 1456, 1457, 1458, 1459, 1460, 1461, 1462, 1463, 1464, 1465, 1468]                                                                                                    | 42          | 73         | public | 3          | 4          |
+-----------+---------+-------------+------------+------------------+--------------------------------+------+-----+-----------------------------+--------------+------------+-----------+-------+----------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------+------------+--------+------------+------------+


Advanced Clustering
-------------------

