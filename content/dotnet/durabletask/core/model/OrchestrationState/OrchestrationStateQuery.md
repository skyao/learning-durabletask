---
title: "OrchestrationStateQuery"
linkTitle: "OrchestrationStateQuery"
weight: 20
date: 2024-01-19
description: >
  OrchestrationStateQuery
---

`src\DurableTask.Core\OrchestrationStateQuery.cs`

*Query class that can be used to filter results from the Orchestration instance store.*

可用于从协调实例存储中过滤结果的查询类。

> 注意: 实例方法不是线程安全的。

## 类定义



```c#
 public class OrchestrationStateQuery    {
    }
```

构造函数

```c#
        public OrchestrationStateQuery()
        {
            FilterMap = new Dictionary<Type, OrchestrationStateQueryFilter>();
        }
```



### FilterMap()



```c#
        public IDictionary<Type, OrchestrationStateQueryFilter> FilterMap { get; private set; }
```



### GetFilters()

获取查询的 primary_filter、collection_of(secondary_filters)

```c#
public Tuple<OrchestrationStateQueryFilter, IEnumerable<OrchestrationStateQueryFilter>> GetFilters()
        {
            ICollection<OrchestrationStateQueryFilter> filters = FilterMap.Values;
            if (filters.Count == 0)
            {
                return null;
            }

            var secondaryFilters = new List<OrchestrationStateQueryFilter>();

            OrchestrationStateQueryFilter primaryFilter = filters.First();
            int primaryFilterPrecedence = SafeGetFilterPrecedence(primaryFilter);

            if (filters.Count > 1)
            {
                foreach (OrchestrationStateQueryFilter filter in filters)
                {
                    int newPrecedence = SafeGetFilterPrecedence(filter);
                    if (newPrecedence > primaryFilterPrecedence)
                    {
                        secondaryFilters.Add(primaryFilter);

                        primaryFilter = filter;
                        primaryFilterPrecedence = newPrecedence;
                    }
                    else
                    {
                        secondaryFilters.Add(filter);
                    }
                }
            }

            return new Tuple<OrchestrationStateQueryFilter, IEnumerable<OrchestrationStateQueryFilter>>(
                primaryFilter, secondaryFilters);
        }
```

