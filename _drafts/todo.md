# 궁금한 것
* marshall
    * byte, string 등 -> xml , json 등
    * 반대는 unmarshall
    * https://strange-developer.tistory.com/47

* WebMvcTest에서 WebMvcConfigurationSupport 사용하는 법

# 정리할 것
* Date
* Spring Test
* Spring Error Handling
* Spring Static Resource
* soft delete and fk
* argument resovle
* maven http block
* spring property
* concurrentMap
* foreign_key_checks & data grip
  * data grip ui이용하면 안됨
  * console 창에서 직접 하면됨...\
  * https://mariadb.com/docs/reference/mdb/system-variables/foreign_key_checks/
* mybatis empty()
* auto_increment 이상하게 나오는 현상
  * information_schema_stats_expiry
* char[] -> String
  * char[].toString() 과 String.valueOf() 가 다르다.
* react
  * 외부에서 class instance 사용하기
    * ref 이용
    ```
    let createIndividualChargerCtrlModalInstane;
    let domContainer = document.querySelector('#contentContainer_chrgrCtrl');
    ReactDOM.render(<CreateIndividualChargerCtrlModal ref={ el => createIndividualChargerCtrlModalInstane = el }/>, domContainer);
    export {CreateIndividualChargerCtrlModal, createIndividualChargerCtrlModalInstane};
    ```
* kafka
  * bridge 네트워크 설정
  * ansible 로 설치