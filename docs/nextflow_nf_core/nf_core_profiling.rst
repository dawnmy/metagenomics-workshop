nf-core pipeline for taxonomy profiling
================

The nf-core/taxpfoiler (https://nf-co.re/taxprofiler) will be used in this training. It consists of serveral best practice profilers: e.g., ``mOTUs`` and ``metaPhlAn``.

----------

- **Download mOTUs databases** if ``db_mOTU`` is not available in ``/vol/mgcourse``

.. code-block:: shell

  cd /vol/mgcourse

  # wget https://openstack.cebitec.uni-bielefeld.de:8080/swift/v1/mg_databases/db_mOTU_v3.1.0.tar.gz
  # tar zvxf db_mOTU_v3.1.0.tar.gz

  mkdir -p output_taxprofiler

- **Set the database path with** ``vi databases.csv``

.. code-block:: shell

  tool,db_name,db_params,db_path
  motus,db_mOTU,,/vol/mgcourse/db_mOTU

- Prepare the compressed fastq files as input:

.. code-block:: shell
  
  cd /vol/mgcourse/WGS-data
  pigz -k read1.fq
  pigz -k read2.fq

- **Prepare sample sheet file with** ``vi samples.csv``

.. code-block:: shell

  sample,run_accession,instrument_platform,fastq_1,fastq_2,fasta
  s1,run1,ILLUMINA,/vol/mgcourse/WGS-data/read1.fq.gz,/vol/mgcourse/WGS-data/read2.fq.gz,

- Activate ``nf_metag`` env
.. code-block:: shell

  conda activate nf_metag


- **Run the pipeline**

.. code-block:: shell

  nextflow run nf-core/taxprofiler \
    -profile docker \
    --input samples.csv \
    --databases databases.csv \
    --outdir output_taxprofiler \
    --run_motus \
    --motus_use_relative_abundance

After the pipeline finished, we can will have all results in the ``output_taxprofiler`` directory.


.. code-block:: shell

  grep -vE '^#' output_taxprofiler/motus/db_mOTU/s1_run1_db_mOTU.out |awk -F"\t" '$3>0'|csvtk sort -tH -k3:r|csvtk pretty -tH|less
