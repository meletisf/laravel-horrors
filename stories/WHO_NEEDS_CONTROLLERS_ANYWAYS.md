# Who needs Controllers Anyways?

### By Meletisf

Someone asks me to add few features on a web app that was meant for a shipping company. After a day or so i get access to the Github repo. I start browsing the code and the first thing i see is a bunch of models sitting in /app. I open them and surprisingly they are well written. They only had defined relationships tho. I went ahead to see the controllers because that's where usually the horror starts. I open /Http/Controllers and i see only the base controller. Oh well.. I wanted to notify the client that the repo was out of date and that he hadn't pushed the controllers. The project was almost ready and that how i knew that there were controllers somewhere. SOMEWHERE. I checked one last time the web.php routes file and it was the default file. I then checked the api.php file. OH_GOD.php.... It took a while to load because it contained 2000 lines. Each route had a closure and everything was inside the api.php file. WHY?????.php


Here is a sample of the route:
(every single route was like that, inside the api.php file)

```php
Route::prefix('/token')->group(function(){
    Route::post('/create', function(Request $request){
        $creds = $request->only('email', 'password');
        try{
            if(!($token = JWTAuth::attempt($creds))){
                return response()->json([
                    'status' => 'error',
                    'msg' => 'Invalid email/password.'
                ]);
            }
        }catch(JWTException $ex){
            return response()->json([
                'status' => 'error',
                'error' => 'Unable to create token.'
            ]);
        }
        return [
            'status' => "success",
            'token' => $token
        ];
    })->middleware('throttle:10,1');
    Route::post('/invalidate', function(Request $request){
        if (JWTAuth::invalidate(JWTAuth::getToken())) {
            return response()->json([
                'status' => 'success'
            ]);
        }
        return response()->json([
            'status' => 'error',
            'msg' => 'Unable to invalidate token'
        ]);
    })->middleware('jwt.verify');
});

```

You might by wondering, how do you secure that?

Well, there is nothing to secure if the route doesn't exist, right?

```php
    if ($user && $user->staff->level >= 1) {
        Route::get("diary", function (Request $r) {
```

With this innovative way you dont need complicated authentication policies.
