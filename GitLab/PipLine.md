```
stages:  
  - build  
  - test  
  - delivery  
  - deploy  
  
build-job:  
  stage: build  
  script:  
    - echo "Start build job"  
    - build-script.sh  
  
test-job:  
  stage: test  
  script:  
    - echo "Start test job"  
    - test-script.sh  
  
delivery-job:  
  stage: delivery  
  script:  
    - echo "Start delivery job"  
    - delivery-script.sh  
  
deploy-job:  
  stage: deploy  
  script:  
    - echo "Start deploy job"  
    - deploy-script.sh
```