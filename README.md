<a href="https://www.w3web.net/roll-up-summary-trigger-on-custom-object/">Go to <b style="color:#ff0000;">more details</b> about this article__  www.w3web.net</a><br/><br/>

trigger childObjTriggerRollUp on childObjTrigger__c (before insert, before update, before delete, after insert, after update, after delete, after undelete) {
    List<childObjTrigger__c> childObjList = new List<childObjTrigger__c>();
   Set<Id> accSetId = new Set<Id>();  
    if(trigger.isBefore){
        system.debug('trigger before#');
    }else if(trigger.isAfter){
       system.debug('trigger after#');
        if(trigger.isDelete){
           childObjList = trigger.old;            
        }else{
            childObjList = trigger.new;
        }
        
        
        
        List<parentObjTrigger__c> updateParent = new List<parentObjTrigger__c>();
        
        for(childObjTrigger__c childObjNewList:childObjList){
            if(childObjNewList.childLookup__c != null){
                accSetId.add(childObjNewList.childLookup__c);
                
                if(trigger.isUpdate){
                   childObjTrigger__c childObjOld = trigger.oldMap.get(childObjNewList.Id);
                  
                    system.debug('childObjOld ' + childObjOld);
                    if(childObjOld.childLookup__c != childObjNewList.childLookup__c){
                        accSetId.add(childObjOld.childLookup__c);
                    }else if(childObjOld.childLookup__c == null){
                        accSetId.add(childObjNewList.childLookup__c);
                    }
                 }
                
            }           
        }
        
        //system.debug('accSetId ' + accSetId); 
        for(parentObjTrigger__c parentSetObj: [Select Id, Name, childRollUp__c, (Select Id, childLookup__c From childObjTriggers__r ) From parentObjTrigger__c Where Id IN:accSetId]){
            List<childObjTrigger__c> childSize = parentSetObj.childObjTriggers__r;
            parentSetObj.childRollUp__c=childSize.size();
            updateParent.add(parentSetObj);
        }
        update updateParent;
        
    }
    
}
