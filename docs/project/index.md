# Defining a reference architecture

Things we need to establish are:

- What should be the priorities? (i.e. what things to monitor?)  
  List of candidates:  
    - ERD endpoint or otherwise forwarding SEDC data off by other means
    - Viz with Graphana, using container with support infrastructure built
    - High frequency/high bandwidth data: live view only, never store
    - Back end database technologies: Retention, reduction, archival 
      considerations
    - Alerting: how to do, considerations
    - Transports: considerations and options
    - Non-web options for using, consuming, transporting, etc....

- What constraints should we consider?  
  i.e. what is a reasonable minimum commitment of hardware or software for the
  reference architecture to require?


