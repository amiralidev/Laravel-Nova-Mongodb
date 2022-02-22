# Laravel-Nova-Mongodb
add mongodb support to laravel nova


## Create

in nova>src>Http>Controllers>ResourceStoreroller.php

change the whole function

```php
public function handle(CreateResourceRequest $request)
{
    $resource = $request->resource();
    $resource::authorizeToCreate($request);
    $resource::validateForCreation($request);
    [$model, $callbacks] = $resource::fill(
        $request, $resource::newModel()
    );
    $this->storeResource($request, $model);
    return response()->json([
        'id' => $model->getKey(),
        'resource' => $model->attributesToArray(),
        'redirect' => $resource::redirectAfterCreate($request, $request->newResourceWith($model)),
    ], 201);
}
```

## Update

in nova>src>Http>Controllers>ResourceUpdateController.php

change the whole function

```php
public function handle(UpdateResourceRequest $request)
{
    $model = $request->findModelQuery()->lockForUpdate()->firstOrFail();
    $resource = $request->newResourceWith($model);
    $resource->authorizeToUpdate($request);
    $resource::validateForUpdate($request, $resource);
    [$model, $callbacks] = $resource::fillForUpdate($request, $model);
    $model->save();
    return response()->json([
        'id' => $model->getKey(),
        'resource' => $model->attributesToArray(),
        'redirect' => $resource::redirectAfterUpdate($request, $resource),
    ]);
}
```



## Fix Search

in nova>src>PerformsQueries.php

change

```php
 foreach (static::searchableColumns() as $column) {
    $query->orWhere(
        $model->qualifyColumn($column),
        $likeOperator,
        static::searchableKeyword($column, $search)
    );
}
```

to

```php
foreach (static::searchableColumns() as $column) {
    $query->orWhere($column, $likeOperator, '%'.$search.'%');
}
```
