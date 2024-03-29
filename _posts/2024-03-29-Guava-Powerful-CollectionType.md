# Guava Powerful CollectionType and CollectionUtilties

## Iterable Filter

```java
ImmutableList<ItemValue> itemValueImmutableList = ImmutableList.of(new ItemValue("label", 0),
                new ItemValue("labelApp", 1), new ItemValue("labelSpan", 1));
// filter
Iterable<ItemValue> itemValues = Iterables.filter(itemValueImmutableList, r -> r.getItemIndex() > 0);
itemValues.forEach(itemValue -> System.out.println(itemValue.itemIndex));

// findFirst
Optional<ItemValue> itemValueOptional = Iterables.tryFind(itemValueImmutableList, r -> r.getItemIndex() > 3);
System.out.println(itemValueOptional.or(new ItemValue("rc", -1)));
```

## Iterable mapping 

```java
Iterable<String> transform = Iterables.transform(itemValueImmutableList, i -> i.value);
transform.forEach(System.out::println);
```

## Iterable getFirst/Last

```java
Integer first = Iterables.getFirst(Arrays.asList(10, 5), 0);

String onlyElement = Iterables.getOnlyElement(Collections.singletonList("occupied"), "defaultValue");
```

## Jdk8 Stream equivalent fluent api

```java
ImmutableList<@NonNull String> immutableList = FluentIterable
    .from(ImmutableList.of(new ItemValue("archive", 1), new ItemValue("flag", 0)))
    .filter(e -> e.getItemIndex() > 0)
    .transform(r -> Strings.repeat(r.getValue(), 3))
    .toList();

Iterables.getFirst(immutableList, "repeatV");

// archivearchivearchive
```

## Lists mapping

```java
ImmutableList<ItemValue> itemValueImmutableList = ImmutableList.of(
      new ItemValue("label", 0),
      new ItemValue("labelApp", 1), new ItemValue("labelSpan", 1)
);

List<String> ItemValueList = Lists.transform(itemValueImmutableList, f -> f.value);

// ["label", "labelApp", "labelSpan"]
```

## Maps differences

```java
ImmutableMap<String, Integer> immutablePackageValue = ImmutableMap.of(
        "sv_package_1", 1,
        "sv_package_2", 1,
        "sv_package_3", 0
);

ImmutableMap<String, Integer> immutablePackageValue2 = ImmutableMap.of(
        "sv_package_1", 1,
        "sv_package_2", 1
        // "sv_package_3", 0
);

// diff two maps
MapDifference<String, Integer> difference = Maps.difference(immutablePackageValue, immutablePackageValue2);
// differing, onLeft and onRight all empty that was meaning are equivalent

System.out.println(difference.entriesDiffering());
System.out.println(difference.entriesOnlyOnLeft());
System.out.println(difference.entriesOnlyOnRight());

// {}
// {sv_package_3=0}
// {}

// filter immutable map by key
Map<String, Integer> svPackage1 = Maps.filterKeys(immutablePackageValue, m -> m.equals("sv_package_1"));
System.out.println(svPackage1);
// {sv_package_1=1}
```
