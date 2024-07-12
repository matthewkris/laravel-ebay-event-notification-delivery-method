# eBay Event Notification Delivery Method handler for Laravel 11

This creates a valid endpoint for Marketplace Account Deletion which is required to access production data. I wrote this for Laravel 11 but can easily be adapted for other versions or frameworks. I thought this would be helpful to put out as the eBay docs are notoriously bad. You should expand the code below to actually handle the deletion request.

## Route
Note - you must support both GET and POST (this was buried in the docs). Exclude verifying the CSRF token for the POST request as they won't be sending one along.
```
Route::any('ebay-account-deletion-end-point', [EbayController::class, 'accountDeletionEndpoint'])
    ->withoutMiddleware([VerifyCsrfToken::class]);
```
## Controller
```
public function accountDeletionEndpoint(Request $request): JsonResponse
{
    $hash = hash_init('sha256');

    $challengeCode = $request->challenge_code;

    // make something up here and use it as the Verification Token on the eBay form, 32-80 chars.
    $verificationToken = 'c2edb7d327ca6ff20abe70751d655efffc4d4320913cadf';

    $endpoint = route('ebay-account-deletion-end-point');

    hash_update($hash, $challengeCode);
    hash_update($hash, $verificationToken);
    hash_update($hash, $endpoint);

    $responseHash = hash_final($hash);

    $obj = (object) [];
    $obj->challengeResponse = $responseHash;

    return response()->json($obj);
}
```
