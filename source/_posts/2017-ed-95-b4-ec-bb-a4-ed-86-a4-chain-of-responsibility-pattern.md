---
title: '[2017해커톤] Chain Of Responsibility pattern'
url: 1452.html
id: 1452
categories:
  - 미분류
date: 2017-08-15 10:28:03
tags:
---

필요 기능

1.  사용자가 입력한 조건으로 데이터가 생성 되게 만드는 기능
2.  해당 데이터의 조건은 여러가지 일 수도 있다.

현재 프로젝트에서 Filter가 실행되는 곳은 TypeHandler의 handle함수이다. 처음 handle에서 어떤 Filter를 사용할지 판단하여 선택하는 방식으로 하게 되었는데 결합이 강해져 결합을 약하게 만들기 위해 사용하게 되었다. FilterManager를 통해 FilterChain을 관리하고, FilterChain은 Filter들을 실행하고, 실행할 Filter들을 추가한다. FilterSelect는 CharacterFilter, NumberFilter 등을 선택한다.   ![스크린샷 2017-08-15 01.15.05.png](https://ahea.files.wordpress.com/2017/08/ec8aa4ed81aceba6b0ec83b7-2017-08-15-01-15-05.png)  

public class FilterManager {

    FilterChain filterChain;

    public FilterManager() {
        filterChain = new FilterChain();
    }

    public void setFilter(Filter filter) {
        filterChain.addFilter(filter);
    }

    public Boolean filter(Object value){
        return filterChain.execute(value);
    }
}

 

public class FilterChain {

    private List<Filter> filters = new ArrayList<Filter>();

    public void addFilter(Filter filter) {
        filters.add(filter);
    }

    public boolean execute(Object value) {
        int index = 0;
        for (Filter filter : filters) {
            if( !filter.filter(value) ) {
                return true;
            }
        }
        return false;

    }
}

 

public class CharacterFilter implements Filter<String> {

    Predicate<String> condition;

    public CharacterFilter(Predicate<String> condition) {
        this.condition = condition;
    }

    @Override
    public Boolean filter(String value) {
        return condition.test(value);
    }

}

 

public class NumberFilter implements Filter<Integer> {

    Predicate<Integer> condition;

    public NumberFilter(Predicate<Integer> condition) {
        this.condition = condition;
    }

    @Override
    public Boolean filter(Integer value) {
        return condition.test(value);
    }

}

 

public class FilterSelect {

    public FilterManager selectFilter(FieldCategory fieldCategory){

        ......
            Filter filter = null;
            if(type.equals(CategoryType.Repo)){
                filterManager.setFilter( addRepoFilter(condition) );
            }else if(type.equals(CategoryType.Random)){
                filterManager.setFilter( addRandomFilter(condition) );
            }else if(type.equals(CategoryType.Select)){
//                filterManager.setFilter( addSelectFilter() );
            }else if(type.equals(CategoryType.Date)){
//                filterManager.setFilter( addDateFilter() );
            }else {
                throw new RuntimeException("category type error");
            }
        ......
        return filterManager;
    }

    private Filter addRepoFilter(String condition){

        ......

        Filter filter = null;

        if(sign.equals("first")){
            filter = new CharacterFilter(o -> o.startsWith(comparison));
        }else if(sign.equals("middle")){
            filter = new CharacterFilter(o -> o.contains(comparison));
        }else if(sign.equals("last")){
            filter = new CharacterFilter(o -> o.endsWith(comparison));
        }else {
            throw new RuntimeException("sign error");
        }

        return filter;
    }

    private Filter addRandomFilter(String condition){

        ......

        Filter filter = null;
        if(sign.equals("<")){
            filter = new NumberFilter(o -> o < comparison);
        }else if(sign.equals("<=")){
            filter = new NumberFilter(o -> o < comparison);
        }else if(sign.equals(">")){
            filter = new NumberFilter(o -> o > comparison);
        }else if(sign.equals(">=")){
            filter = new NumberFilter(o -> o >= comparison);
        }else if(sign.equals("==")){
            filter = new NumberFilter(o -> o == comparison);
        }else if(sign.equals("!=")){
            filter = new NumberFilter(o -> o != comparison);
        }else {
            throw new RuntimeException("sign error");
        }

        return filter;
    }
}

 

public class TypeHandler {

    public List<List<ResultData>> handle(List<FieldCategory> fieldCategoryList, Integer rowNumber){
        ......
                if(fieldCategory.getConditions() != null){
                    //filter insert
                    FilterSelect filterSelect = new FilterSelect();
                    FilterManager filterManager = filterSelect.selectFilter(fieldCategory);

                    filterManager.filter(value);
                }

               ......
    }

}

참고 자료 : [http://wildpup.cafe24.com/archives/596](http://wildpup.cafe24.com/archives/596) [http://solarisailab.com/archives/1330](http://solarisailab.com/archives/1330)